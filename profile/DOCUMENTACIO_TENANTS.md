## Documentació tenants

Per fer que els models siguin tenant-aware he afegir la columna "tenant_id" a les taules.

### Actualització

He desfet aquesta modificació ja que aquesta serveix quan tenim una base de dades amb diferents esquemes per a cada tenant (companyia)!

El que he de fer és crear una nova carpeta 'database/migrations/tenant', i moure totes les migracions que utilitzen tenant a aquesta.

Desprès he de configurar 'config/tenanacy.php' i ajustar la configuraciñi per a la creació de bases de dades.

---

```php

// config/tenancy.php

'tenant_database_manager' => Stancl\Tenancy\TenantDatabaseManagers\MySQLDatabaseManager::class, // O PostgreSQLDatabaseManager si fas servir Postgres

'database' => [
    'central_connection' => env('DB_CONNECTION', 'mysql'), // La teva connexió central

    // Aquest és el prefix per a les bases de dades dels tenants
    'prefix' => 'tenant_',
    'suffix' => '',

    // Aquesta és la connexió que s'usarà com a "plantilla"
    // El paquet copiarà aquesta configuració i només canviarà el nom de la base de dades
    'template_connection_name' => 'mysql', // O 'pgsql'
],

'migration_parameters' => [
    // Aquí li diem a la comanda tenants:migrate on són les migracions dels tenants
    '--path' => [database_path('migrations/tenant')],
    '--realpath' => true,
],

'seeder_parameters' => [
    '--class' => 'DatabaseSeeder', // O el teu seeder de tenants
    // '--path' => [database_path('seeders/tenant')], // Si tens seeders separats
],

```

### Pendent

Revisar pas 4 -->

Pas 4: Configurar el Creador de Bases de Dades
El paquet necessita un usuari de base de dades amb permisos per crear altres bases de dades. Assegura't que l'usuari de la teva connexió principal (central_connection) tingui el privilegi CREATE DATABASE.

Per exemple, a MySQL: GRANT ALL PRIVILEGES ON *.* TO 'el_teu_usuari'@'localhost';

Pas 5: Com Funciona Ara?
Executar Migracions Centrals: Quan executis php artisan migrate, només s'executaran les migracions de database/migrations a la teva base de dades central.

Crear un Tenant: Quan creïs un tenant nou, el paquet farà el següent automàticament:

$tenant = Tenant::create(['id' => 'client1']);
$tenant->domains()->create(['domain' => 'client1.localhost']);
El paquet crearà una nova base de dades anomenada, per exemple, tenant_client1.
El paquet executarà automàticament les migracions de database/migrations/tenant dins d'aquesta nova base de dades tenant_client1.
Executar Migracions per a Tenants Existents: Si necessites aplicar noves migracions a tots els tenants que ja existeixen, has d'utilitzar la comanda:

php artisan tenants:migrate
Aquest enfocament és més net si necessites un aïllament de dades estricte i et permet no haver de pensar a afegir tenant_id a tot arreu.
