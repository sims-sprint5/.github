# Multi-Tenant System — SIMS Back

Technical documentation for the multi-tenant architecture using PostgreSQL schemas.

---

## Architecture Overview

The project uses **[stancl/tenancy v3](https://tenancyforlaravel.com/)** with the **PostgreSQL Schema** strategy.

```
PostgreSQL database
├── schema: public  (central data)
│   ├── tenants
│   ├── domains
│   ├── superadmins
│   └── personal_access_tokens
│
├── schema: tenant_company1  (isolated per tenant)
│   ├── users
│   ├── vehicles
│   ├── reservations
│   ├── tickets
│   ├── geofences
│   └── vehicle_geofence_logs
│
└── schema: tenant_company2
    └── (same tables)
```

Tenant identification is by **subdomain**:
```
http://company1.lvh.me:8000/api/v1/...     → tenant = company1
http://company2.lvh.me:8000/api/v1/...     → tenant = company2
http://localhost:8000/api/v1/superadmin/... → central (SuperAdmin)
```

---

## Tenant Lifecycle

When a SuperAdmin calls `POST /api/v1/superadmin/tenants`:

```
1. Request validation
2. Tenant::create()
   └─► [Event: TenantCreated]
         ├─ CreateDatabase  → creates schema tenant_{id}
         └─ MigrateDatabase → runs all migrations in database/migrations/tenant/
3. $tenant->domains()->create()  → registers subdomain
4. SeedDatabase::dispatchSync()  → runs TenantDatabaseSeeder (admin user + demo data)
5. Returns 201 with access URL
```

> The seed runs explicitly in the controller (step 4), NOT inside the pipeline (step 2). If seeding fails, the tenant and domain already exist and 201 is returned anyway. The error is logged to `storage/logs/laravel.log`.

When a tenant is deleted (`DELETE /api/v1/superadmin/tenants/{id}`):
```
TenantDeleted → DeleteDatabase → drops the entire tenant_{id} schema
```

---

## Environment Variables

| Variable | Local example | Production example | Description |
|----------|--------------|-------------------|-------------|
| `APP_URL` | `http://localhost:8000` | `https://api.sims.com` | Central app base URL |
| `TENANT_BASE_DOMAIN` | `lvh.me` | `sims.com` | Root domain for tenant subdomains |
| `APP_DOMAIN` | *(not needed locally)* | `api.sims.com` | Central domain (added to `central_domains`) |

**Local URL generation:**
```
APP_URL=http://localhost:8000
TENANT_BASE_DOMAIN=lvh.me
→ Tenant URL: http://company1.lvh.me:8000
```

**Production URL generation:**
```
APP_URL=https://api.sims.com
TENANT_BASE_DOMAIN=sims.com
APP_DOMAIN=api.sims.com
→ Tenant URL: https://company1.sims.com
```

---

## Authentication Endpoints

### SuperAdmin (central)

| Method | Endpoint |
|--------|----------|
| POST | `/api/v1/superadmin/auth/login` |
| GET  | `/api/v1/superadmin/auth/me` |
| POST | `/api/v1/superadmin/auth/logout` |

Middlewares: `auth:sanctum`, `ensure.superadmin`

### Tenant user

| Method | Endpoint |
|--------|----------|
| POST | `http://{id}.lvh.me:8000/api/v1/auth/login` |
| GET  | `http://{id}.lvh.me:8000/api/v1/auth/me` |
| POST | `http://{id}.lvh.me:8000/api/v1/auth/logout` |

The tenant is detected from the subdomain. All DB operations automatically target the `tenant_{id}` schema.

---

## Routes: Central vs. Tenant

| File | Scope | Middlewares |
|------|-------|-------------|
| `routes/api.php` | SuperAdmin central | `auth:sanctum`, `ensure.superadmin` |
| `routes/tenant.php` | Tenant via subdomain | `InitializeTenancyBySubdomain`, `PreventAccessFromCentralDomains` |
| `routes/web.php` | Public | none |

---

## Tenant Migrations

All tenant migrations go in `database/migrations/tenant/`.  
Migrations in `database/migrations/` only affect the central `public` schema.

```bash
# Create a tenant migration
php artisan make:migration create_xxxx_table --path=database/migrations/tenant

# Apply to all tenants
docker compose exec api php artisan tenants:migrate

# Apply to a specific tenant
docker compose exec api php artisan tenants:migrate --tenants=company1
```

---

## Tenant Seeders

`database/seeders/TenantDatabaseSeeder.php` runs automatically when a tenant is created via API. It creates:

- 1 admin user (credentials provided at creation time)
- 5 users with `user` role
- 8 vehicles
- 6 reservations
- 10 tickets
- 5 geofences

`admin_password` is discarded from the central DB after seeding.

```bash
# Re-run seeder manually (creates additional data, does not wipe)
docker compose exec api php artisan tenants:seed --tenants=company1
```

---

## Adding a New Module

```bash
# 1. Tenant migration
php artisan make:migration create_xxxx_table --path=database/migrations/tenant

# 2. Model (no need to specify connection — tenancy switches it automatically)
php artisan make:model XxxModel

# 3. API controller
php artisan make:controller Api/XxxController --api

# 4. Register route in routes/tenant.php
Route::apiResource('xxxx', XxxController::class);

# 5. Apply to existing tenants
docker compose exec api php artisan tenants:migrate
```

---

## Local Development DNS

Tenant URLs use `lvh.me` which always resolves to `127.0.0.1` — no configuration needed.

If working offline:
```bash
echo "127.0.0.1 company1.lvh.me" | sudo tee -a /etc/hosts
```

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `relation "tenants" does not exist` | `php artisan migrate --force` |
| `Could not find driver` | Check `sims_postgres` container is running and `DB_*` vars are correct |
| Subdomain not resolving (`ERR_NAME_NOT_RESOLVED`) | Check internet connection (lvh.me DNS is external) or add to `/etc/hosts` |
| `php artisan tinker` permission error in Docker | `docker compose exec -e HOME=/tmp api php artisan tinker` |
| Seed failed but tenant exists | Re-run: `php artisan tenants:seed --tenants={id}` |

---

## Tests

```bash
# Run tenant test suite inside the container
docker compose exec api php artisan test --testsuite=Tenant
```
