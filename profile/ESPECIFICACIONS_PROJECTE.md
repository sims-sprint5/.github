# Documentació Tècnica

## 1. Introduction

### 1.1 Purpose of the document
This document defines the technical analysis and design of the application before starting development. It serves as a guide, but is not entirely rigid.

---

### 1.2 Project scope
This project is focused on developing a multi-tenant web application, where client companies can manage their vehicle fleets.

---

## 2. General description of the application

### 2.1 Application objective
The application has a clear objective for the client: to allow management of their vehicle fleet, improve control, and obtain benefits through shared vehicle services (car sharing). It also offers a B2C (business-to-consumer) ticketing system for communication with customers.

---

### 2.2 User roles
Main roles of the application:

- **Superuser** (`super_user`): manages global statistics for all companies and administers global resources and services, e.g. databases, admin accounts, API integrations (maps), and subdomains.
- **Company administrator** (`company_admin`): manages the company to which they are assigned; has access only to their company's statistics. In the vehicles section, they can view, create, update, and delete vehicles. Manages the ticketing system with customers and the administration of users in their company.
- **Worker** (`worker`): manages tickets with customers and can view and update vehicle information.
- **Customer** (`customer`): can rent a vehicle and create tickets to request help or support.

---

## 4. Use cases

### 4.1 Use case: Vehicle management

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

### 4.2 Use case: User management
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

### 4.3 Use case: Reservation management
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

### 4.4 Use case: IoT data visualization
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


### 4.5 Use case: Ticket management
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

## 5. System architecture

### 5.1 Architecture type

The application will follow a client-server architecture with REST API communication. To facilitate scalability and maintainability, a modular approach will be adopted for both frontend and backend.

- Frontend: SPA application with reusable components and domain modules (Vue 3 + TypeScript).
- Backend: modular architecture based on MVC adapted to modules (MMVC), where each module encapsulates models, controllers, services, routes, and migrations.

ESTRUCTURA MODULAR FRONTEND:

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
├─ components/                       # Componentes base compartits
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
├─ router/                           # Configuració de rutas
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

ESTRUCTURA BACKEND MVC:

```
laravel-backend/
├─ app/                           # Núcli de l'aplicació
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
│  └─ console.php                # Comandos d'Artisan
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

Notes for the backend:
- Each module registers its routes and migrations, using Service and Providers to initialize modules.

---
### 

---

### 5.2 Communication between components

Communication between frontend and backend will be mainly via REST API with JSON. Main considerations:

- Authentication: CSRF tokens.
- API versioning: use `/api/v1/...` for compatibility.
- Errors: responses with HTTP code, internal code, and user-friendly message.
- Real-time: WebSockets or pub/sub services for telemetry and real-time notifications if needed.


## 6. Technologies used

- Frontend: Vite + Vue3 + TypeScript
- Backend: Laravel
- Database: Postgres
- Version control: Git
- Other tools: Github Projects, PgAdmin

---

## 7. Data model


### 7.1 Main database entities

Below are the main tables with fields, types, and constraints.

#### Table `users`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, autoincrement, not null |
| email | varchar(70) | not null, unique |
| pwd | text | not null |
| name | varchar(50) | not null |
| created_at | timestamp | not null |
| updated_at | timestamp |  |
| deleted_at | timestamp |  |

#### Table `companies`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, autoincrement, not null |
| created_by | int | not null |
| name | varchar(50) | not null |
| description | varchar(255) |  |
| cif | varchar(25) | not null, unique |
| db_conexion | varchar(255) | not null |
| db_user | varchar(255) | not null |
| db_pwd | text | not null |
| created_at | timestamp | not null |
| updated_at | timestamp |  |
| deleted_at | timestamp |  |

**Notes:**
- Fields marked as "encrypted" (db_conexion, db_user, db_pwd) must be stored securely (e.g. encrypted in the database or managed by a secrets system).
- `created_by` is a foreign key referencing `users.id` and its integrity must be validated.
- Add indexes to `email` and `cif` to improve queries and ensure uniqueness.


---

### 7.2 Tenant database entities

[BDD SIMS](https://dbdiagram.io/d/Fleetly_Main-696e57e5d6e030a0247b5b8a)
[BDD Company](https://dbdiagram.io/d/Fleetly2-0-6977b8ccbd82f5fce2a99d25)

Below are the tenant-specific tables (multi-tenant schema) with fields, types, and constraints.

#### Table `users`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| email | varchar(70) | unique, not null |
| password | text | not null |
| role_id | int | not null |
| name | varchar(50) | not null |
| surname | varchar(50) |  |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |

#### Table `roles`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| name | varchar(50) | not null, unique |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |

#### Table `permissions`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| name | varchar(50) | not null, unique |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |


#### Table `roles_permissions`

| Field | Type | Constraints |
|-------|------|-------------|
| role_id | int | not null |
| permission_id | int | not null |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |

Note: a composite PK `(role_id, permission_id)` is recommended to ensure uniqueness.

#### Table `vehicle_types`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| name | varchar(50) | not null, unique |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |


#### Table `vehicles`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| license | varchar(15) | not null, unique |
| status | enum | values: `available`, `using`, `stopped` |
| vehicle_type | int | not null |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |

#### Table `payment_forms`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| name | varchar(50) | not null |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |


#### Table `rentals`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| user_id | int | not null |
| vehicle_id | int | not null |
| payment_form_id | int | not null |
| exit_point | varchar(255) | not null |
| drop_off_point | varchar(255) | not null |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |


#### Table `tickets`

| Field | Type | Constraints |
|-------|------|-------------|
| id | int | PK, not null, autoincrement |
| created_by | int | not null |
| assigned_id | int |  |
| vehicle_id | int | not null |
| status | enum | values: `solved`, `open`, `in_progress` |
| created_at | timestamp | not null |
| updated_at | timestamp | not null |
| deleted_at | timestamp |  |

---

**Remarks:**
- It is recommended to declare indexes for `email`, `license`, and `cif` in the corresponding tables.
- For enums (`status`), ensure their definition in the migration/DB and state management in the backend.
- Add audit mechanisms (`created_by`/`updated_by`) if detailed traceability is required.

## 8. Security

Security is a priority at all layers of the application: authentication, authorization, data, infrastructure, and processes. The following recommended measures and best practices are listed below.

- **Authentication**
    - Store passwords with a strong algorithm (bcrypt, Argon2) and unique salts.
    - Manage sessions with tokens.

- **Authorization and access control**
    - Implement RBAC (roles) and checks at the service and data level.
    - Apply the principle of least privilege.

- **Validation and sanitization**
    - Validate all input data (server and client) and use parameterized queries/ORM to prevent SQL injection.
    - Encode output to prevent XSS.

- **HTTP protections and secure headers**
    - Implement CSRF tokens for state-changing operations.


- **Testing and continuous review**
    - Integrate integration tests in the CI pipeline.


---

## 9. Conclusion
This document serves as an initial guide for developing the application. Here the minimum initial requirements are collected (they may change over time). This document also allows new people to join the project without going in blind.

To maintain the project, the following steps must be followed:
- **Prioritize requirements**
- **Maintain the data model** (whenever possible).
- **Coordinate deployment**
- **Maintain consistency with the code conventions** [CodingCoventions](CodingConventions.md)
```