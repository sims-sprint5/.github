# Documentació Tècnica

## 1. Introducció

### 1.1 Pròposit del document
Aquest document defineix l'analisis tècnic i el disseny de l'aplicació avans de començar el desenvolupament. Serveix com a guia.
---

### 1.2 Abast del projecte
Aquest projecte està enfocat en el desenvolupament d'una aplicació web multiinquilí, on les empreses clientes poden gestionar les seves flotes de vehicles.
---

## 2. Descripció general de l'aplicació

### 2.1 Objectiu de l'aplicació
L'aplicació té un objectiu clar per al client: permetre la gestió de la seva flota de vehicles, millorar-ne el control i obtenir beneficis mitjançant serveis de vehicle compartit (car sharing). També ofereix un sistema de ticketing B2C (business-to-consumer) per a la comunicació amb els clients.

---

### 2.2 Rols d'usuari
Rols principals de l'aplicació:

- **Superusuari** (`super_user`): gestiona les estadístiques globals de totes les empreses i administra els recursos i serveis globals, p. ex. bases de dades, comptes d'administrador, integracions d'API (mapes) i subdominis.
- **Administrador d'empresa** (`company_admin`): gestiona l'empresa a la qual està assignat; només té accés a les estadístiques de la seva empresa. A la secció de vehicles, pot veure, crear, actualitzar i eliminar vehicles. Gestiona el sistema de tiquets amb els clients i l'administració d'usuaris de la seva empresa.
- **Treballador** (`worker`): gestiona els tiquets amb els clients i pot veure i actualitzar la informació dels vehicles.
- **Client** (`customer`): pot llogar un vehicle i crear tiquets per sol·licitar ajuda o suport.

---

## 3. Requisits funcionals

Funcionalitats que el sistema ha de complir.

RF-001: Gestió d'usuaris: registrar-se, autenticar-se (iniciar sessió) i restablir la contrasenya.
RF-002: Control d'identitat i permisos: assignació de rols i permisos (superusuari, administrador, treballador, client).
RF-003: Gestió d'usuaris administratius: crear, veure, actualitzar i eliminar usuaris i assignar rols.
RF-004: Gestió de vehicles: crear, veure, actualitzar i eliminar vehicles (inclou dades tècniques i estat operacional).
RF-005: Gestió d'empreses (multi-tenant): crear, veure,actualitzar i eliminar empreses/clients.
RF-006: Provisió d'empreses: automatitzar la creació de l'estructura necessària per a cada empresa (base de dades/espai,subdomini/pàgina dedicada, punts d'accés, configuracions inicials).
RF-007: Lloguers/reserves de vehicles: reservar, confirmar, modificar i cancel·lar lloguers; gestionar la disponibilitat.
RF-008: Pagaments i facturació: integrar pagaments (passarel·la), generar factures i gestionar els estats de pagament.
RF-009: Sistema de tiquets: crear, assignar, comentar, tancar i eliminar tiquets; historial de comunicació client-suport.
RF-010: Notificacions: enviar notificacions per correu electrònic/push/SMS per a esdeveniments rellevants (reserves, tiquets, alertes).
RF-011: Informes i estadístiques: generar taulers i informes per empresa i a nivell global (ús de la flota, ingressos, incidències).
RF-012: Registre d'auditoria: registrar operacions crítiques i activitats dels usuaris per a la seva traçabilitat.
RF-013: Configuració de l'empresa: paràmetres personalitzables per empresa (polítiques, preus, plans, recursos disponibles).
RF-014: Cerca i filtres: cercar i filtrar vehicles, empreses i reserves per diversos criteris.
RF-015: Gestió de permisos de recursos: controlar l'accés als recursos (p. ex., visibilitat de vehicles per empresa).
RF-016: Recuperació i gestió d'errors: mostrar errors clars i permetre reintentar operacions crítiques/sensibles(p. ex., pagaments fallits).

---

## 4. Casos d'ús

### 4.1 Cas d'ús: Gestió de vehicles

                      +-------------------------------+
                      | Administrator / Fleet Manager |
                      +-------------------------------+
                                   |
                                   |
                                   v
                           +-----------------+
                           | Vehicle Management |
                           +-----------------+
                           | - Add           |
                           | - Edit          |
                           | - Delete        |
                           | - View          |
                           +-----------------+
                                   |
                                   |
                                   v
                           +-----------------+
                           | Result          |
                           +-----------------+
                           | - Vehicle created |
                           | - Vehicle updated |
                           | - Vehicle deleted |
                           | - Vehicle list   |
                           |   displayed      |
                           +-----------------+

### 4.2 Cas d'ús: Gestió d'usuaris
                      +----------------------+
                      |   Administrator      |
                      +----------------------+
                               |
                               |
                               v
                       +--------------------+
                       | User Management    |
                       +--------------------+
                       | - Create user      |
                       | - Edit user        |
                       | - Delete user      |
                       | - View list        |
                       +--------------------+
                               |
                               |
                               v
                       +------------------------+
                       | Result                 |
                       +------------------------+
                       | - User registered      |
                       | - User updated         |
                       | - User deleted         |
                       | - User list updated    |
                       +------------------------+

### 4.3 Cas d'ús: Gestió de reserves
                      +-------------------------+
                      |    User / Citizen       |
                      +-------------------------+
                               |
                               |
                               v
                       +--------------------+
                       | Reservation        |
                       +--------------------+
                       | - View available   |
                       |   vehicles         |
                       | - Reserve vehicle  |
                       | - Cancel reservation |
                       +--------------------+
                                 |
                                 |
                                 v
                       +---------------------------+
                       | Result                    |
                       +---------------------------+
                       | - Reservation confirmed   |
                       | - Reservation cancelled   |
                       | - Event list updated      |
                       +---------------------------+

### 4.4 Cas d'ús: Visualització de dades IoT
                      +---------------------------+
                      | Administrator / Analyst   |
                      +---------------------------+
                                 |
                                 |
                                 v
                         +-------------------+
                         | IoT Data Display  |
                         +-------------------+
                         | - View GPS        |
                         | - View battery    |
                         | - View vehicle    |
                         |   status          |
                         | - Sensor alerts   |
                         +-------------------+
                                 |
                                 |
                                 v
                         +---------------------------+
                         | Result                    |
                         +---------------------------+
                         | - Dashboard updated       |
                         | - Real-time information   |
                         | - All vehicles displayed  |
                         +---------------------------+

### 4.5 Cas d'ús: Gestió de tiquets
                      +-------------------------------+
                      | User / Citizen / Admin        |
                      +-------------------------------+
                                 |
                                 |
                                 v
                         +-------------------+
                         | Ticket Management |
                         +-------------------+
                         | - Create ticket   |
                         | - View ticket     |
                         | - Assign ticket   |
                         | - Resolve ticket  |
                         | - Close ticket    |
                         +-------------------+
                                 |
                                 |
                                 v
                         +---------------------------+
                         | Result                    |
                         +---------------------------+
                         | - Ticket created          |
                         | - Ticket updated          |
                         | - Ticket resolved/closed  |
                         | - Ticket list updated     |
                         +---------------------------+

---

## 5. Arquitectura del sistema

### 5.1 Tipus d'arquitectura

L'aplicació seguirà una arquitectura client-servidor amb comunicació mitjançant una API REST. Per facilitar l'escalabilitat i la mantenibilitat, s'adoptarà un enfocament modular tant per al frontend com per al backend.

- Frontend: aplicació SPA amb components reutilitzables i mòduls de domini (Vue 3 + TypeScript).
- Backend: arquitectura modular basada en MVC adaptada a mòduls (MMVC), on cada mòdul encapsula models, controladors, serveis, rutes i migracions.

ARQUITECTURA MODULAR FRONTEND:

```
src/
├─ assets/                           # Recursos estàtics
├─ modules/                          # Dominis modulars
│  ├─ auth/
│  │  ├─ views/
│  │  ├─ components/
│  │  ├─ composables/
│  │  ├─ services/
│  │  ├─ types/
│  │  ├─ utils/
│  │  └─ routes.ts
│  ├─ tickets/
│  │  ├─ views/
│  │  ├─ components/
│  │  ├─ services/
│  │  ├─ types/
│  │  ├─ utils/
│  │  └─ routes.ts
│  ├─ users/
│  │  ├─ views/
│  │  ├─ components/
│  │  ├─ composables/
│  │  ├─ services/
│  │  ├─ types/
│  │  ├─ utils/
│  │  └─ routes.ts
│  ├─ dashboard/
│  │  ├─ views/
│  │  └─ routes.ts
│  └─ settings/
│     ├─ views/
│     ├─ composables/
│     └─ routes.ts
├─ components/                       # Components base compartits
│  ├─ BaseButton.vue
│  ├─ BaseCard.vue
│  ├─ BaseInput.vue
│  ├─ BaseModal.vue
│  ├─ BaseTable.vue
│  ├─ BaseToast.vue
│  └─ LanguageSelector.vue
├─ layouts/                          # Layouts
│  ├─ AppLayout.vue
│  └─ components/
│     ├─ Navbar.vue
│     └─ Sidebar.vue
├─ router/                           # Configuració de rutes
│  └─ index.ts
├─ shared/                           # Utilitats globals
│  ├─ composables/
│  ├─ services/
│  ├─ types/
│  └─ utils/
├─ locales/                          # Internacionalització (i18n)
│  ├─ ca.json
│  ├─ en.json
│  └─ es.json
├─ App.vue
├─ main.ts
├─ i18n.ts
└─ style.css
```

ARQUITECTURA BACKEND MVC:

```
laravel-backend/
├─ app/                           # Nucli de l'aplicació
│  ├─ Http/
│  │  ├─ Controllers/
│  │  │  ├─ Api/                 # Controladors API
│  │  │  │  ├─ AuthController.php
│  │  │  │  ├─ UserController.php
│  │  │  │  ├─ VehicleController.php
│  │  │  │  ├─ ReservationController.php
│  │  │  │  ├─ TicketController.php
│  │  │  │  └─ GeofenceController.php
│  │  │  └─ Controller.php        # Controlador base
│  │  └─ Middleware/
│  ├─ Models/                    # Entitats (Eloquent ORM)
│  │  ├─ User.php
│  │  ├─ Vehicle.php
│  │  ├─ Reservation.php
│  │  ├─ Ticket.php
│  │  ├─ Geofence.php
│  │  └─ VehicleGeofenceLog.php
│  └─ Providers/
│     └─ AppServiceProvider.php
├─ config/                        # Configuració de l'aplicació
│  ├─ app.php
│  ├─ auth.php
│  ├─ database.php
│  ├─ cache.php
│  └─ ...
├─ database/                      # Base de dades
│  ├─ migrations/                # Esquemes de taules
│  │  ├─ create_users_table.php
│  │  ├─ create_vehiculos_table.php
│  │  ├─ create_reservas_table.php
│  │  ├─ create_tickets_table.php
│  │  ├─ create_geofences_table.php
│  │  └─ create_vehicle_geofence_logs_table.php
│  ├─ factories/                 # Generadors de dades de prova
│  │  └─ UserFactory.php
│  └─ seeders/                   # Pobladors de BD
│     ├─ DatabaseSeeder.php
│     └─ DatabaseTestSeeder.php
├─ routes/                        # Definició de rutes
│  ├─ api.php                    # Rutes API (/api/...)
│  ├─ web.php                    # Rutes web tradicionals
│  └─ console.php                # Comandes d'Artisan
├─ tests/                         # Tests
│  ├─ Feature/
│  └─ Unit/
├─ bootstrap/                     # Bootstrap de l'app
├─ storage/                       # Emmagatzematge
├─ public/                        # Carpeta pública (index.php)
├─ resources/                     # Recursos (vistes, CSS, JS)
├─ artisan                        # CLI de Laravel
├─ composer.json                  # Dependències PHP
├─ docker-compose.yml            # Configuració Docker
└─ phpunit.xml                   # Configuració de tests
```

Notes per al backend:
- Cada mòdul registra les seves rutes i migracions, utilitzant Serveis i Proveïdors per inicialitzar els mòduls.

---

### 5.2 Comunicació entre components

La comunicació entre el frontend i el backend es farà principalment mitjançant una API REST amb JSON. Consideracions principals:

- Autenticació: Tokens CSRF.
- Versionat d'API: utilitzar `/api/v1/...` per a la compatibilitat.
- Errors: respostes amb codi HTTP, codi intern i missatge amigable per a l'usuari.
- Temps real: WebSockets o serveis pub/sub per a telemetria i notificacions en temps real si és necessari.


## 6. Tecnologies utilitzades

- Frontend: Vite + Vue3 + TypeScript
- Backend: Laravel
- Base de dades: Postgres
- Control de versions: Git
- Altres eines: Github Projects, PgAdmin

---

## 7. Model de dades


### 7.1 Entitats principals de la base de dades

A continuació es mostren les taules principals amb camps, tipus i restriccions.

#### Taula `users`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, autoincrement, no nul |
| email | varchar(70) | no nul, únic |
| pwd | text | no nul |
| name | varchar(50) | no nul |
| created_at | timestamp | no nul |
| updated_at | timestamp |  |
| deleted_at | timestamp |  |

#### Taula `companies`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, autoincrement, no nul |
| created_by | int | no nul |
| name | varchar(50) | no nul |
| description | varchar(255) |  |
| cif | varchar(25) | no nul, únic |
| db_conexion | varchar(255) | no nul |
| db_user | varchar(255) | no nul |
| db_pwd | text | no nul |
| created_at | timestamp | no nul |
| updated_at | timestamp |  |
| deleted_at | timestamp |  |

**Notes:**
- Els camps marcats com a "xifrats" (db_conexion, db_user, db_pwd) s'han d'emmagatzemar de forma segura (p. ex., xifrats a la base de dades o gestionats per un sistema de secrets).
- `created_by` és una clau externa que fa referència a `users.id` i s'ha de validar la seva integritat.
- Afegir índexs a `email` i `cif` per millorar les consultes i garantir la unicitat.


---

### 7.2 Entitats de la base de dades del tenant

[BDD SIMS](https://dbdiagram.io/d/Fleetly_Main-696e57e5d6e030a0247b5b8a)
[BDD Company](https://dbdiagram.io/d/Fleetly2-0-6977b8ccbd82f5fce2a99d25)

A continuació es mostren les taules específiques del tenant (esquema multi-tenant) amb camps, tipus i restriccions.

#### Taula `users`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| email | varchar(70) | únic, no nul |
| password | text | no nul |
| role_id | int | no nul |
| name | varchar(50) | no nul |
| surname | varchar(50) |  |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |

#### Taula `roles`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| name | varchar(50) | no nul, únic |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |

#### Taula `permissions`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| name | varchar(50) | no nul, únic |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |


#### Taula `roles_permissions`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| role_id | int | no nul |
| permission_id | int | no nul |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |

Nota: es recomana una PK composta `(role_id, permission_id)` per garantir la unicitat.

#### Taula `vehicle_types`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| name | varchar(50) | no nul, únic |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |


#### Taula `vehicles`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| license | varchar(15) | no nul, únic |
| status | enum | valors: `available`, `using`, `stopped` |
| vehicle_type | int | no nul |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |

#### Taula `payment_forms`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| name | varchar(50) | no nul |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |


#### Taula `rentals`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| user_id | int | no nul |
| vehicle_id | int | no nul |
| payment_form_id | int | no nul |
| exit_point | varchar(255) | no nul |
| drop_off_point | varchar(255) | no nul |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |


#### Taula `tickets`

| Camp | Tipus | Restriccions |
|-------|------|-------------|
| id | int | PK, no nul, autoincrement |
| created_by | int | no nul |
| assigned_id | int |  |
| vehicle_id | int | no nul |
| status | enum | valors: `solved`, `open`, `in_progress` |
| created_at | timestamp | no nul |
| updated_at | timestamp | no nul |
| deleted_at | timestamp |  |

---

**Observacions:**
- Es recomana declarar índexs per a `email`, `license` i `cif` a les taules corresponents.
- Per als enums (`status`), assegureu-ne la definició a la migració/BD i la gestió d'estats al backend.
- Afegir mecanismes d'auditoria (`created_by`/`updated_by`) si es requereix una traçabilitat detallada.

## 8. Seguretat

La seguretat és una prioritat a totes les capes de l'aplicació: autenticació, autorització, dades, infraestructura i processos. A continuació s'enumeren les mesures i millors pràctiques recomanades.

- **Autenticació**
    - Emmagatzemar contrasenyes amb un algorisme fort (bcrypt, Argon2) i sals úniques.
    - Gestionar sessions amb tokens.

- **Autorització i control d'accés**
    - Implementar RBAC (rols) i comprovacions a nivell de servei i dades.
    - Aplicar el principi de mínim privilegi.

- **Validació i sanejament**
    - Validar totes les dades d'entrada (servidor i client) i utilitzar consultes parametritzades/ORM per prevenir la injecció de SQL.
    - Codificar la sortida per prevenir XSS.

- **Proteccions HTTP i capçaleres segures**
    - Implementar tokens CSRF per a operacions que canvien l'estat.


- **Proves i revisió contínua**
    - Integrar proves d'integració al pipeline de CI.


---

## 9. Conclusió
Aquest document serveix com a guia inicial per al desenvolupament de l'aplicació. Aquí es recullen els requisits mínims inicials (poden canviar amb el temps). Aquest document també permet que noves persones s'incorporin al projecte sense anar a cegues.

Per mantenir el projecte, cal seguir els passos següents:
- **Prioritzar requisits**
- **Mantenir el model de dades** (sempre que sigui possible).
- **Coordinar el desplegament**
- **Mantenir la coherència amb les convencions de codi** [CodingCoventions](CodingConventions.md)
