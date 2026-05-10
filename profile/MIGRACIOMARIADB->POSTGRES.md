# PostgreSQL Migration Guide

This document details all the changes made to migrate the project from **MariaDB/MySQL to PostgreSQL**.

---

## 📋 Summary of Changes

Configuration files, migrations, Docker, seeders and documentation have been modified to implement PostgreSQL with PostGIS instead of MariaDB.

---

## 🔧 Changes by File

### 1. **Dockerfile** (previously: `DockerFile`)

**Changes:**
- Base image change: `php:8.4-cli` → `php:8.4-fpm`
- Dependency installation: `mariadb-client` → `libpq-dev`
- PHP extensions: `pdo_mysql mysqli` → `pdo_pgsql`
- Added apt cache cleanup: `&& rm -rf /var/lib/apt/lists/*`
- Permission configuration: `chown -R www-data:www-data storage bootstrap`
- Use of FPM (FastCGI Process Manager) instead of CLI for better performance

**Why:**
- `php:8.4-fpm` is the standard for production and Docker
- `pdo_pgsql` is the driver to connect with PostgreSQL
- Permissions prevent bootstrap/cache errors

---

### 2. **docker-compose.yml**

**Main changes:**

#### Database
```yaml
# BEFORE: MariaDB 11.2
db:
  image: mariadb:11.2
  container_name: mariadb_blink
  ports:
    - "3306:3306"
  environment:
    MYSQL_DATABASE: blink_sprint4_equip3
    MYSQL_USER: blink_sprint4_equip3
    MYSQL_PASSWORD: root
    MYSQL_ROOT_PASSWORD: password

# AFTER: PostgreSQL with PostGIS
postgres:
  image: postgis/postgis:15-3.3
  container_name: sims_postgres
  ports:
    - "${DB_PORT:-5432}:5432"
  environment:
    POSTGRES_USER: ${DB_USERNAME:-sims_user}
    POSTGRES_PASSWORD: ${DB_PASSWORD:-sims_password}
    POSTGRES_DB: ${DB_DATABASE:-sims}
  healthcheck:
    test: ["CMD-SHELL", "pg_isready -U ${DB_USERNAME:-sims_user}"]
```

#### Laravel Application
```yaml
# BEFORE
app:
  environment:
    - DB_CONNECTION=mysql
    - DB_PORT=3306

# AFTER
api:
  environment:
    DB_CONNECTION: pgsql
    DB_PORT: 5432
```

#### New additions
- **pgAdmin 4**: Visual tool to manage PostgreSQL
- **Healthcheck**: Verifies PostgreSQL is ready before starting the app
- **Dynamic environment variables**: `${DB_USERNAME:-sims_user}` allows flexibility
- **Updated network**: `sims_network` (more descriptive)

---

### 3. **config/database.php**

```php
// BEFORE
'default' => env('DB_CONNECTION', 'sqlite'),

// AFTER
'default' => env('DB_CONNECTION', 'pgsql'),
```

The `pgsql` driver was already defined in the file, only the default changed.

---

### 4. **.env.example**

```env
# BEFORE
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=

# AFTER
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=sims
DB_USERNAME=sims_user
DB_PASSWORD=sims_password
```

**Changes:** Values uncommented, specific to PostgreSQL, descriptive names.

---

### 5. **database/migrations/2026_01_21_145551_create_vehiculos_table.php**

```php
// BEFORE (incompatible with PostgreSQL)
$table->year('year')->nullable();

// AFTER (compatible with both DBs)
$table->unsignedSmallInteger('year')->nullable();
```

**Why:** `YEAR` is a type exclusive to MySQL/MariaDB. `unsignedSmallInteger` works in PostgreSQL and stores years correctly (0-65535).

---

### 6. **README.md**

Documentation update:

| Section | Change |
|---------|--------|
| Tech Stack | MariaDB 11.2 → PostgreSQL 16 |
| Environment Variables | `DB_PORT=3306` → `DB_PORT=5432` |
| Ports | 3306 → 5432 |
| Containers | `mariadb_blink` → `postgres_sims`, `db` → `postgres` |

---

### 7. **.dockerignore**

New file created with exclusions for Docker builds:
- Git files (`.git`, `.gitignore`, `.gitattributes`)
- Docker files (`.dockerignore`, `Dockerfile*`, `docker-compose*`)
- Node/PHP dependencies (`node_modules`, `vendor`)
- Logs, cache, backups
- PostgreSQL data directories

---

### 8. **.gitignore**

**Changes made:**

```gitignore
# BEFORE
.env.*                  # Ignores all .env.*
docker-compose.yml      # Explicitly ignored
DockerFile              # Ignored
composer.lock           # Ignored

# AFTER
.env.*                  # Still ignores .env.backup, .env.production, etc.
!.env.example           # BUT: allows .env.example
# docker-compose.yml    # REMOVED: now committed
# DockerFile            # REMOVED: now committed
# composer.lock         # REMOVED: now committed (reproducible build)
```

**Reason:** These files are essential for other developers to pull and run `docker compose up` without additional configuration.

---

## 📊 Models & Seeders

**No changes needed.** The models already had:
- `protected $primaryKey = 'user_id'` (compatible with PostgreSQL)
- `protected $fillable = [...]` (works the same)
- Relationships with `foreignId` (DB-agnostic)

**DatabaseSeeder.php:** Works as-is. Laravel/Eloquent handles DB differences automatically.

---

## 🚀 Implementation Steps

### 1. Initial setup
```bash
# Clone repo
git clone <url> && cd sims-back-3

# Copy .env
cp .env.example .env

# Generate APP_KEY
php artisan key:generate

# Install dependencies
composer install
```

### 2. Docker
```bash
# Build and start
docker compose up -d --build

# Verify PostgreSQL is ready
docker compose exec postgres pg_isready -U sims_user
```

### 3. Database
```bash
# Migrations and seeders
php artisan migrate:fresh --seed

# Or with verbose to see details
php artisan migrate:fresh --seed -v
```

### 4. pgAdmin (optional)
```
URL: http://localhost:5050
Email: admin@example.com
Password: admin

Register server:
- Host: postgres
- Port: 5432
- Database: sims
- Username: sims_user
- Password: sims_password
```

---

## 🔍 PostgreSQL vs MariaDB Advantages

| Feature | PostgreSQL | MariaDB |
|---------|-----------|---------|
| **PostGIS** | ✅ Native | ❌ Limited extension |
| **JSON** | ✅ Native type + indexes | ⚠️ Limited |
| **Scalability** | ✅ Excellent | ⚠️ Good |
| **Transactions** | ✅ MVCC (better concurrency) | ⚠️ Traditional locks |
| **Replication** | ✅ Built-in | ⚠️ Requires setup |
| **Complex types** | ✅ Arrays, Ranges, Domains | ❌ Not supported |

**For this project:** PostGIS is critical for geofencing. PostgreSQL is the best option.

---

## 📝 Configuration by Environment

### Development (local)
```env
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=sims
DB_USERNAME=sims_user
DB_PASSWORD=sims_password
```

### Docker
```env
DB_HOST=postgres          # Service name in docker-compose
DB_PORT=5432
DB_DATABASE=sims
DB_USERNAME=sims_user
DB_PASSWORD=sims_password
```

### Production
```env
DB_HOST=<RDS/external IP>
DB_PORT=5432
DB_DATABASE=sims_prod
DB_USERNAME=<secure user>
DB_PASSWORD=<secure password>
```

---

## 🐛 Troubleshooting

### "relation does not exist"
- Make sure you are pointing to `sims`, not `postgres`
- Correct schema: `public.users` (not `sims.public.users`)

### pgAdmin does not show tables
- **Maintenance database** must be `sims` (not `postgres`)
- Refresh the tree: right-click on **Tables** → **Refresh**

### Migrations do not run
- Verify: `docker compose exec postgres psql -U sims_user -d sims -c "\dt"`
- If empty: `php artisan migrate:fresh`

### Laravel cannot connect
- Check healthcheck: `docker compose exec postgres pg_isready -U sims_user`
- Logs: `docker compose logs api`

---

## 📚 References

- [PostgreSQL Official](https://www.postgresql.org/docs/)
- [PostGIS Manual](https://postgis.net/docs/)
- [Laravel Database Configuration](https://laravel.com/docs/12.x/database)
- [Docker Compose](https://docs.docker.com/compose/)

---

**Last updated:** February 24, 2026

**Status:** ✅ Completed and tested
