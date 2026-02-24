# PostgreSQL Migration Guide


Este documento detalla todos los cambios realizados para migrar el proyecto de **MariaDB/MySQL a PostgreSQL**.


---


## 📋 Resumen de Cambios


Se han modificado archivos de configuración, migraciones, Docker, seeders y documentación para implementar PostgreSQL con PostGIS en lugar de MariaDB.


---


## 🔧 Cambios por Archivo


### 1. **Dockerfile** (antes: `DockerFile`)


**Cambios:**
- Cambio de base image: `php:8.4-cli` → `php:8.4-fpm`
- Instalación de dependencias: `mariadb-client` → `libpq-dev`
- Extensiones PHP: `pdo_mysql mysqli` → `pdo_pgsql`
- Adición de limpieza de caché apt: `&& rm -rf /var/lib/apt/lists/*`
- Configuración de permisos: `chown -R www-data:www-data storage bootstrap`
- Uso de FPM (FastCGI Process Manager) en lugar de CLI para mejor rendimiento


**Por qué:**
- `php:8.4-fpm` es el estándar para producción y Docker
- `pdo_pgsql` es el driver para conectar con PostgreSQL
- Los permisos evitan errores de bootstrap/cache


---


### 2. **docker-compose.yml**


**Cambios principales:**


#### Base de datos
```yaml
# ANTES: MariaDB 11.2
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


# DESPUÉS: PostgreSQL con PostGIS
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


#### Aplicación Laravel
```yaml
# ANTES
app:
  environment:
    - DB_CONNECTION=mysql
    - DB_PORT=3306


# DESPUÉS
api:
  environment:
    DB_CONNECTION: pgsql
    DB_PORT: 5432
```


#### Adiciones nuevas
- **pgAdmin 4**: Herramienta visual para gestionar PostgreSQL
- **Healthcheck**: Verifica que PostgreSQL esté listo antes de iniciar la app
- **Variables de entorno dinámicas**: `${DB_USERNAME:-sims_user}` permite flexibilidad
- **Red actualizada**: `sims_network` (más descriptivo)


---


### 3. **config/database.php**


```php
// ANTES
'default' => env('DB_CONNECTION', 'sqlite'),


// DESPUÉS
'default' => env('DB_CONNECTION', 'pgsql'),
```


El driver `pgsql` ya estaba definido en el archivo, solo cambió el default.


---


### 4. **.env.example**


```env
# ANTES
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=


# DESPUÉS
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=sims
DB_USERNAME=sims_user
DB_PASSWORD=sims_password
```


**Cambios:** Valores descomentados, específicos para PostgreSQL, nombres descriptivos.


---


### 5. **database/migrations/2026_01_21_145551_create_vehiculos_table.php**


```php
// ANTES (incompatible con PostgreSQL)
$table->year('year')->nullable();


// DESPUÉS (compatible con ambas BD)
$table->unsignedSmallInteger('year')->nullable();
```


**Por qué:** `YEAR` es un tipo exclusivo de MySQL/MariaDB. `unsignedSmallInteger` funciona en PostgreSQL y guarda años correctamente (0-65535).


---


### 6. **README.md**


Actualización de documentación:


| Sección | Cambio |
|---------|--------|
| Stack Tecnológico | MariaDB 11.2 → PostgreSQL 16 |
| Variables de Entorno | `DB_PORT=3306` → `DB_PORT=5432` |
| Puertos | 3306 → 5432 |
| Containers | `mariadb_blink` → `postgres_sims`, `db` → `postgres` |


---


### 7. **.dockerignore**


Creado nuevo archivo con exclusiones para Docker builds:
- Archivos de Git (`.git`, `.gitignore`, `.gitattributes`)
- Archivos de Docker (`.dockerignore`, `Dockerfile*`, `docker-compose*`)
- Dependencias Node/PHP (`node_modules`, `vendor`)
- Logs, cache, backups
- Directorios de datos PostgreSQL


---


### 8. **.gitignore**


**Cambios realizados:**


```gitignore
# ANTES
.env.*                  # Ignora todos los .env.*
docker-compose.yml      # Ignorado explícitamente
DockerFile              # Ignorado
composer.lock           # Ignorado


# DESPUÉS
.env.*                  # Sigue ignorando .env.backup, .env.production, etc.
!.env.example           # BUT: permite .env.example
# docker-compose.yml    # REMOVED: ahora se sube
# DockerFile            # REMOVED: ahora se sube
# composer.lock         # REMOVED: ahora se sube (build reproducible)
```


**Razón:** Estos archivos son esenciales para que otros desarrolladores puedan hacer pull y ejecutar `docker compose up` sin configuración adicional.


---


## 📊 Modelos & Seeders


**Sin cambios necesarios.** Los modelos ya tenían:
- `protected $primaryKey = 'user_id'` (compatible con PostgreSQL)
- `protected $fillable = [...]` (funciona igual)
- Relaciones con `foreignId` (agnóstico de BD)


**DatabaseSeeder.php:** Funciona tal cual. Laravel/Eloquent maneja las diferencias de BD automáticamente.


---


## 🚀 Pasos de Implementación


### 1. Configuración inicial
```bash
# Clonar repo
git clone <url> && cd sims-back-3


# Copiar .env
cp .env.example .env


# Generar APP_KEY
php artisan key:generate


# Instalar dependencias
composer install
```


### 2. Docker
```bash
# Build y start
docker compose up -d --build


# Verificar que PostgreSQL está listo
docker compose exec postgres pg_isready -U sims_user
```


### 3. Base de datos
```bash
# Migraciones y seeders
php artisan migrate:fresh --seed


# O con verbose para ver detalles
php artisan migrate:fresh --seed -v
```


### 4. pgAdmin (opcional)
```
URL: http://localhost:5050
Email: admin@example.com
Password: admin


Registrar servidor:
- Host: postgres
- Port: 5432
- Database: sims
- Username: sims_user
- Password: sims_password
```


---


## 🔍 Ventajas de PostgreSQL vs MariaDB


| Feature | PostgreSQL | MariaDB |
|---------|-----------|---------|
| **PostGIS** | ✅ Nativo | ❌ Extensión limitada |
| **JSON** | ✅ Tipo nativo + índices | ⚠️ Limitado |
| **Escalabilidad** | ✅ Excelente | ⚠️ Buena |
| **Transactions** | ✅ MVCC (mejor concurrencia) | ⚠️ Locks tradicionales |
| **Replicación** | ✅ Built-in | ⚠️ Requiere setup |
| **Tipos complejos** | ✅ Arrays, Ranges, Domains | ❌ No soporta |


**Para este proyecto:** PostGIS es crítico para geofencing. PostgreSQL es la mejor opción.


---


## 📝 Configuración por Entorno


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
DB_HOST=postgres          # Nombre del servicio en docker-compose
DB_PORT=5432
DB_DATABASE=sims
DB_USERNAME=sims_user
DB_PASSWORD=sims_password
```


### Production
```env
DB_HOST=<RDS/IP externa>
DB_PORT=5432
DB_DATABASE=sims_prod
DB_USERNAME=<usuario seguro>
DB_PASSWORD=<contraseña segura>
```


---


## 🐛 Troubleshooting


### "relation does not exist"
- Asegúrate de apuntar a `sims`, no a `postgres`
- Schema correcto: `public.users` (no `sims.public.users`)


### pgAdmin no muestra tablas
- **Maintenance database** debe ser `sims` (no `postgres`)
- Refresh el árbol: click derecho en **Tables** → **Refresh**


### Migraciones no corren
- Verifica: `docker compose exec postgres psql -U sims_user -d sims -c "\dt"`
- Si vacío: `php artisan migrate:fresh`


### Laravel no se conecta
- Verifica healthcheck: `docker compose exec postgres pg_isready -U sims_user`
- Logs: `docker compose logs api`


---


## 📚 Referencias


- [PostgreSQL Official](https://www.postgresql.org/docs/)
- [PostGIS Manual](https://postgis.net/docs/)
- [Laravel Database Configuration](https://laravel.com/docs/12.x/database)
- [Docker Compose](https://docs.docker.com/compose/)


---


**Última actualización:** 24 de febrero de 2026


**Estado:** ✅ Completado y probado







