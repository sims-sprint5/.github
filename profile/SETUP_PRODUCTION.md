# Production Setup — SIMS Back

## Prerequisites

- Linux server with Docker >= 20.10 and Docker Compose >= 2.0
- SSH access to the server
- GitHub repository with the following secrets configured:

| Secret | Description |
|--------|-------------|
| `DEPLOY_SSH_KEY_B64` | SSH private key encoded in base64 |
| `DEPLOY_HOST` | Server IP or hostname |
| `DEPLOY_PORT` | SSH port (default: 22) |
| `DEPLOY_USER` | SSH user |
| `VEHICLE_SUBSYSTEM_KEY` | API key for the IoT subsystem |
| `IOT_MONGO_URI` | MongoDB URI for the IoT subsystem |
| `IOT_API_KEY` | API key for the IoT FastAPI service |

---

## 1. Generate a Deploy SSH Key

```bash
ssh-keygen -t ed25519 -f ~/.ssh/deploy_key

# Encode for GitHub secret
cat ~/.ssh/deploy_key | base64 -w 0

# Copy public key to the server
echo "$(cat ~/.ssh/deploy_key.pub)" >> ~/.ssh/authorized_keys
```

Add the base64 output as the `DEPLOY_SSH_KEY_B64` GitHub secret.

---

## 2. First-Time Server Setup

```bash
ssh -i ~/.ssh/deploy_key <user>@<host>

# Create Docker network (required, external to Compose)
docker network create sims_network

# Clone the repository
git clone git@github.com:sims-sprint5/sims-back.git /var/www/sims-back
cd /var/www/sims-back

# Copy and edit environment file
cp .env.example .env
nano .env
```

### Minimum `.env` values to set

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://your-domain.com
APP_DOMAIN=your-domain.com
TENANT_BASE_DOMAIN=your-domain.com
FRONTEND_URL=https://your-frontend.com

DB_PASSWORD=strong_password_here

SUPERADMIN_NAME="Super Admin"
SUPERADMIN_EMAIL=admin@your-domain.com
SUPERADMIN_PASSWORD=change_this

STRIPE_KEY=pk_live_...
STRIPE_SECRET=sk_live_...
```

---

## 3. Automatic Deploy (CI/CD)

Every push to `main` triggers the GitHub Actions workflow which:

1. Runs Laravel Pint (linter) and PHPUnit tests
2. SSHes into the server
3. Pulls latest code and rebuilds the Docker image
4. Also pulls and rebuilds the IoT subsystem (`~/subsistema`)
5. Runs central migrations
6. Runs seeders (creates SuperAdmin + initial tenant)
7. Runs tenant migrations

```bash
git push origin main   # deploy is automatic
```

Check progress at: GitHub → Actions → latest run

---

## 4. Running Commands Manually

```bash
ssh -i ~/.ssh/deploy_key <user>@<host>
cd /var/www/sims-back
export COMPOSE_FILE=docker-compose.yml

# Container status
docker compose ps

# Laravel logs
docker compose logs -f api

# Artisan commands
docker compose exec -T api php artisan migrate --force
docker compose exec -T api php artisan tenants:migrate --force
docker compose exec -T api php artisan db:seed --force
docker compose exec -T api php artisan optimize
docker compose exec -T api php artisan cache:clear
```

---

## 5. Creating a Tenant via API

```bash
# 1. Get SuperAdmin token
TOKEN=$(curl -s -X POST https://your-domain.com/api/v1/superadmin/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@your-domain.com","password":"your_password"}' \
  | jq -r '.token')

# 2. Create tenant
curl -X POST https://your-domain.com/api/v1/superadmin/tenants \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "company1",
    "name": "Company One",
    "admin_email": "admin@company1.your-domain.com",
    "admin_password": "admin1234"
  }'
```

---

## 6. Database Backup & Restore

```bash
# Backup
docker compose exec sims_postgres pg_dump -U sims_user sims > backup_$(date +%Y%m%d).sql

# Restore
docker compose exec -T sims_postgres psql -U sims_user sims < backup.sql
```

---

## 7. DNS Configuration

For multi-tenant subdomains, create a wildcard DNS record:

```
Type: A
Name: *
Value: YOUR_SERVER_IP
TTL: 3600
```

---

## 8. Common Issues

| Error | Cause | Fix |
|-------|-------|-----|
| `relation "tenants" does not exist` | Central migrations not run | `php artisan migrate --force` |
| `APP_KEY` not set | Key not generated | `php artisan key:generate --show` → add to `.env` |
| Container exits immediately | Check logs | `docker compose logs api --tail=100` |
| 503 on tenant routes | Tenant DB not initialized | `php artisan tenants:migrate --force` |
| Login returns 401 | Wrong credentials or expired token | Check SuperAdmin seeder ran |

---

## 9. IoT Subsystem

The IoT subsystem (FastAPI + MongoDB) is deployed automatically alongside the backend. It lives at `~/subsistema` on the server and runs as container `iot-api` on `sims_network`.

- WebSocket endpoint: `/ws/vehicle/{vehicle_id}` (via nginx proxy at `/api/ws/`)
- Actuator control: `POST /api/actuator/{vehicle_id}/on|off` (requires `X-API-Key` header)
- Temperature data: `GET /api/laravel/temperatures/latest` (requires `X-API-Key` header)

If the Raspberry Pi cannot connect via WebSocket, the proxy chain (OpenResty → Apache → Docker nginx) must have WebSocket support enabled. See `DEBUGGING.md`.
