# 🔐 Funcionalitats del sistema – explicació simple

Aquest document explica **de manera molt fàcil** què pot fer cada tipus d'usuari dins del sistema.

---

## 👤 Usuari

👉 És el client normal de l'aplicació.

✔ Pot registrar-se, iniciar sessió i canviar la seva contrasenya.
✔ Pot actualitzar el seu perfil.
✔ Pot veure els vehicles disponibles i el seu calendari d'ocupació.
✔ Pot crear reserves i pagar via Stripe.
✔ Pot renovar les seves reserves.
✔ Pot veure, modificar o cancel·lar **només les seves pròpies reserves**.
✔ Pot veure i crear els seus propis tickets.
✔ Pot enviar i rebre missatges en els seus tickets.
✔ Pot veure geofences en el mapa.
✔ Pot usar el chatbot.

❌ No pot gestionar altres usuaris ni el sistema.

### Endpoints

🔑 AUTENTICACIÓ
- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/logout`
- `POST /api/v1/auth/logout-all` *(tancar totes les sessions)*
- `GET /api/v1/auth/me`
- `PATCH /api/v1/auth/me` *(actualitzar perfil)*
- `POST /api/v1/auth/change-password`

🚙 VEHICLES
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`
- `GET /api/v1/vehicles-calendar` *(vehicles amb calendari d'ocupació)*
- `GET /api/v1/disponibilitat/{id}` *(disponibilitat d'un vehicle)*

📅 RESERVES
- `GET /api/v1/reservations` *(només pròpies)*
- `GET /api/v1/reservations/{id}` *(només pròpia)*
- `POST /api/v1/reservations`
- `PUT /api/v1/reservations/{id}` *(només pròpia)*
- `DELETE /api/v1/reservations/{id}` *(cancel·lar pròpia)*
- `GET /api/v1/reservations/user/{userId}` *(només el seu ID)*
- `GET /api/v1/reservations/check-availability` *(comprovar disponibilitat)*
- `POST /api/v1/reservations/checkout` *(iniciar pagament Stripe)*
- `POST /api/v1/reservations/{id}/renewal-intent` *(renovar reserva via Stripe)*

🎫 TICKETS
- `GET /api/v1/tickets` *(només propis)*
- `GET /api/v1/tickets/{id}` *(només propi)*
- `POST /api/v1/tickets`
- `GET /api/v1/tickets/user/{userId}` *(els seus tickets)*
- `GET /api/v1/tickets/{ticket}/messages` *(historial de missatges)*
- `POST /api/v1/tickets/{ticket}/messages` *(enviar missatge)*

🗺️ GEOFENCES *(només lectura)*
- `GET /api/v1/geofences`
- `GET /api/v1/geofences/{id}`

🤖 CHATBOT
- `POST /api/v1/chat/ask`

---

## 🛠️ Admin

👉 És el gestor del sistema.

Té tot el que pot fer un usuari i, a més:

### 👥 Usuaris
✔ Veure, crear, modificar i eliminar usuaris.
❌ No pot eliminar administradors ni super administradors.

### 🚙 Vehicles
✔ Crear, modificar i eliminar vehicles.
✔ Veure reserves d'un vehicle.
✔ Actualitzar la ubicació del vehicle.
✔ Sincronitzar la disponibilitat de tots els vehicles.

### 📅 Reserves
✔ Veure totes les reserves.
✔ Canviar l'estat d'una reserva.

### 🎫 Tickets
✔ Gestionar tickets:
- Modificar i eliminar.
- Assignar-los.
- Canviar estat.

### 🌍 Geofencing
✔ Crear, modificar i eliminar zones geogràfiques.
✔ Revisar registres de vehicles dins de zones.
✔ Comprovar si un vehicle està dins d'una zona.

### Endpoints

👥 USUARIS
- `GET /api/v1/users`
- `GET /api/v1/users/{id}`
- `POST /api/v1/users`
- `PUT /api/v1/users/{id}`
- `DELETE /api/v1/users/{id}` *(no pot eliminar admins o super admins)*

🚙 VEHICLES
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`
- `POST /api/v1/vehicles`
- `PUT /api/v1/vehicles/{id}`
- `DELETE /api/v1/vehicles/{id}`
- `GET /api/v1/vehicles/{id}/reservations`
- `PATCH /api/v1/vehicles/{id}/location`
- `POST /api/v1/vehicles/sync-all-availability`

📅 RESERVES
- `GET /api/v1/reservations`
- `GET /api/v1/reservations/{id}`
- `GET /api/v1/reservations/user/{userId}`
- `PATCH /api/v1/reservations/{id}/status`

🎫 TICKETS
- `GET /api/v1/tickets`
- `GET /api/v1/tickets/{id}`
- `PUT /api/v1/tickets/{id}`
- `DELETE /api/v1/tickets/{id}`
- `PATCH /api/v1/tickets/{id}/assign`
- `PATCH /api/v1/tickets/{id}/status`

🗺️ GEOFENCING
- `GET /api/v1/geofences`
- `GET /api/v1/geofences/{id}`
- `POST /api/v1/geofences`
- `PUT /api/v1/geofences/{id}`
- `DELETE /api/v1/geofences/{id}`
- `GET /api/v1/geofences/{id}/logs`
- `POST /api/v1/geofences/check-vehicle`

---

## 👑 Super Admin

👉 És l'administrador de totes les Companyies (Tenants).

✔ Iniciar sessió al seu panell propi.
✔ Crear i eliminar superadministradors.
✔ Crear, modificar i eliminar companyies (tenants).

### Endpoints

🔑 AUTENTICACIÓ SUPERADMIN
- `POST /api/v1/superadmin/auth/login`
- `GET /api/v1/superadmin/auth/me`
- `POST /api/v1/superadmin/auth/logout`

👥 SUPERADMINISTRADORS
- `GET /api/v1/superadmin/admins`
- `POST /api/v1/superadmin/admins`
- `DELETE /api/v1/superadmin/admins/{id}`

🏢 COMPANYIES (TENANTS)
- `GET /api/v1/superadmin/tenants`
- `GET /api/v1/superadmin/tenants/{id}`
- `POST /api/v1/superadmin/tenants`
- `PUT /api/v1/superadmin/tenants/{id}`
- `DELETE /api/v1/superadmin/tenants/{id}`

---

## 💳 Stripe Webhooks

Gestionats internament pel sistema:
- `POST /api/v1/stripe/webhook` *(confirmació de pagaments i renovacions)*

---

# 📊 Matriu Resum de Permisos

### LLEGENDA
✅ Total: Accés complet.
👤 Parcial: Només sobre dades pròpies.
❌ Cap: Sense accés.

| Funcionalitat / Recurs              | Usuari | Admin | Super Admin |
|-------------------------------------|:------:|:-----:|:-----------:|
| Autenticació (Login/Register)       | ✅     | ✅    | ✅          |
| Actualitzar perfil                  | ✅     | ✅    | ❌          |
| Veure Vehicles                      | ✅     | ✅    | ❌          |
| Gestionar Vehicles (CRUD)           | ❌     | ✅    | ❌          |
| Crear Reserves i Pagar (Stripe)     | 👤     | ✅    | ❌          |
| Renovar Reserves (Stripe)           | 👤     | ✅    | ❌          |
| Gestionar Tickets (Incidències)     | 👤     | ✅    | ❌          |
| Missatgeria en Tickets              | 👤     | ✅    | ❌          |
| Veure Geofences (mapa)              | ✅     | ✅    | ❌          |
| Gestionar Geofences (CRUD + logs)   | ❌     | ✅    | ❌          |
| Chatbot                             | ✅     | ✅    | ❌          |
| Gestionar Usuaris (Clients)         | ❌     | ✅    | ❌          |
| Gestionar Superadministradors       | ❌     | ❌    | ✅          |
| Gestionar Companyies (Tenants)      | ❌     | ❌    | ✅          |

---

**Versió:** 2.0
**Última actualització:** 08/05/2026
