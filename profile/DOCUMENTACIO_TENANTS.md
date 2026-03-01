## Documentació tenants

### Requisits dels tenants

- Base de dades amb esquemes x tenant.
- Identificació dels Tenants segons el domini.


### Procediment

1. /config/tenancy.php

Línia 47 — Canviar tenant_database_manager de MySQLDatabaseManager a PostgreSQLSchemaManager::class. Això indica al paquet que creï esquemes dins la mateixa BD en comptes de bases de dades noves.
Línia 73 — Al bloc database.managers.pgsql, comentar PostgreSQLDatabaseManager i descomentar PostgreSQLSchemaManager. Aquesta és la línia que realment s'utilitza per gestionar la creació/eliminació d'esquemes.
Línia 63 — Canviar prefix de 'tenant' a 'tenant_' (amb guió baix) perquè els esquemes es diguin tenant_abc123 en lloc de tenantabc123.

2. Crear el Tenant model.

3. Moure migracions que utilitzen tenants de /migrations a /migrations/tenant


4. Moure rutes API de /routes/api.php a /routes/tenant.php

5. A TenancyServiceProvider.php:139-149, el mètode makeTenancyMiddlewareHighestPriority() usa Illuminate\Contracts\Http\Kernel que ja no existeix a Laravel 12 (que usa app.php). Cal:

Eliminar el mètode makeTenancyMiddlewareHighestPriority() del TenancyServiceProvider.
Afegir la prioritat de middleware directament a app.php dins del callback withMiddleware, usant $middleware->priority([...]) o $middleware->prepend(...).

6. He corretgit les variables d'entorn dels Subdominis

Al fitxer .env he afegit/modificat:

SESSION_DOMAIN=.localhost — Perquè les cookies funcionin entre subdominis.
SANCTUM_STATEFUL_DOMAINS=localhost,*.localhost — Perquè Sanctum reconegui peticions des de subdominis com a stateful.
Verificar que DB_CONNECTION=pgsql, DB_DATABASE=sims, i les credencials de PostgreSQL coincideixen amb el docker-compose.yml.

7. Canvi al document docker-compose.yml

environment:
  DB_HOST: 127.0.0.1 per postgres.

  Per què postgres? Dins de la xarxa Docker (sims_network), els contenidors es comuniquen pel nom del servei definit al docker-compose.yml, no per IP. El servei de base de dades es diu postgres.

### Proves

1. Crear tenant

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

2. Crear domini del tenant

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