## Tenant Documentation
### Tenant Requirements
- Database with schemas per tenant.
- Tenant identification based on domain.
### Procedure
1. /config/tenancy.php
Line 47 — Change tenant_database_manager from MySQLDatabaseManager to PostgreSQLSchemaManager::class. This tells the package to create schemas within the same DB instead of new databases.
Line 73 — In the database.managers.pgsql block, comment out PostgreSQLDatabaseManager and uncomment PostgreSQLSchemaManager. This is the line actually used to manage schema creation/deletion.
Line 63 — Change prefix from 'tenant' to 'tenant_' (with underscore) so schemas are named tenant_abc123 instead of tenantabc123.
2. Create the Tenant model.
3. Move migrations that use tenants from /migrations to /migrations/tenant
4. Move API routes from /routes/api.php to /routes/tenant.php
5. In TenancyServiceProvider.php:139-149, the makeTenancyMiddlewareHighestPriority() method uses Illuminate\Contracts\Http\Kernel which no longer exists in Laravel 12 (which uses app.php). You need to:
Remove the makeTenancyMiddlewareHighestPriority() method from TenancyServiceProvider.
Add middleware priority directly in app.php inside the withMiddleware callback, using $middleware->priority([...]) or $middleware->prepend(...).
6. Fixed Subdomain environment variables
In the .env file I added/modified:
SESSION_DOMAIN=.localhost — So cookies work across subdomains.
SANCTUM_STATEFUL_DOMAINS=localhost,*.localhost — So Sanctum recognizes requests from subdomains as stateful.
Verify that DB_CONNECTION=pgsql, DB_DATABASE=sims, and the PostgreSQL credentials match the docker-compose.yml.
7. Change in docker-compose.yml
environment:
  DB_HOST: 127.0.0.1 to postgres.
  Why postgres? Inside the Docker network (sims_network), containers communicate using the service name defined in docker-compose.yml, not by IP. The database service is called postgres.
### Tests
1. Create tenant
```php
> $tenant = \App\Models\Tenant::create(['id' => 'empresa1']);
> $tenant = \App\Models\Tenant::find('empresa1');
= App\Models\Tenant {#6611
    id: "empresa1",
    created_at: "2026-03-01 11:30:51",
    updated_at: "2026-03-01 11:30:51",
    data: null,
    tenancy_db_name: "tenant_empresa1",
  }
```
2. Create tenant domain
```php
> $tenant->domains()->create(['domain' => 'empresa1']);
> $tenant->domains()->get();
= Illuminate\Database\Eloquent\Collection {#6701
    all: [
      Stancl\Tenancy\Database\Models\Domain {#6697
        id: 1,
        domain: "empresa1",
        tenant_id: "empresa1",
        created_at: "2026-03-01 11:35:18",
        updated_at: "2026-03-01 11:35:18",
      },
    ],
  }
```
---
## Adaptation for Roles System
### Created the SuperAdmin Model
Why? The superadmin must be able to authenticate and generate Sanctum tokens, just like the User. By extending Authenticatable instead of the basic Model, Laravel knows it is an authenticatable user.
### Created the central migration
Why? This table lives in the public (central) schema, not inside any tenant. Separating it from the tenants' users table ensures superadmins never get mixed with end users.
### Configured the superadmins provider in auth.php
Why? Sanctum needs to know which model to use to validate tokens. Without this provider, auth:sanctum would always look in the tenants' users table, never in superadmins.
### Created SuperAdminAuthController
Why? The Superadmin has its own separate authentication flow. Note user('superadmins') — it explicitly tells Laravel to look for the authenticated user in the superadmins provider, not in the users one.
### Created Tenant Controller
### Created SuperAdminSeeder
-> Document tests...
