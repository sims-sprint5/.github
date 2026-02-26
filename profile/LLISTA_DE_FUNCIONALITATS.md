# 🔐 Funcionalitats del sistema – explicació simple

Aquest document explica **de manera molt fàcil** què pot fer cada tipus d’usuari dins del sistema.

---

## 👤 Usuari

👉 És el client normal de l’aplicació.

✔ Pot registrar-se, iniciar sessió i canviar la seva contrasenya.  
✔ Pot veure els vehicles disponibles.  
✔ Pot crear reserves.  
✔ Pot veure, modificar o cancel·lar **només les seves pròpies reserves**.  
✔ Pot veure i crear els seus propis tickets.

❌ No pot gestionar altres usuaris ni el sistema.

### Endpoints

🔑 AUTENTICACIÓ
- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/logout`
- `GET /api/v1/auth/me`
- `POST /api/v1/auth/change-password`

🚙 VEHICLES
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`

📅 RESERVES
- `GET /api/v1/reservations` *(només pròpies)*
- `GET /api/v1/reservations/{id}` *(només pròpia)*
- `POST /api/v1/reservations`
- `PUT /api/v1/reservations/{id}` *(només pròpia)*
- `DELETE /api/v1/reservations/{id}` *(cancel·lar pròpia)*
- `GET /api/v1/reservations/user/{userId}` *(només el seu ID)*

🎫 TICKETS
- `GET /api/v1/tickets` *(només propis)*
- `GET /api/v1/tickets/{id}` *(només propi)*
- `POST /api/v1/tickets`

---

## 🛠️ Admin

👉 És el gestor del sistema.

Té tot el que pot fer un usuari i, a més:

### 👥 Usuaris
✔ Veure i crear usuaris.  
✔ Modificar usuaris.  
❌ No pot eliminar administradors ni super administradors.

### 🚙 Vehicles
✔ Crear, modificar i eliminar vehicles.  
✔ Veure reserves d’un vehicle.  
✔ Actualitzar la ubicació del vehicle.

### 📅 Reserves
✔ Veure totes les reserves.  
✔ Canviar l’estat d’una reserva.

### 🎫 Tickets
✔ Gestionar tickets:  
- Assignar-los.  
- Canviar estat.  
- Modificar o eliminar.

### 🌍 Geofencing
✔ Veure zones geogràfiques controlades.  
✔ Revisar registres de vehicles dins de zones.  
✔ Comprovar si un vehicle està dins d’una zona.

### 🔧 Control del sistema
✔ Veure logs del sistema.  
✔ Bloquejar comptes d’usuaris normals.  
✔ Restablir contrasenyes.  
✔ Veure estadístiques del sistema.  
✔ Reassignar vehicles si hi ha problemes.


### Endpoints

👥 USUARIS
- `GET /api/v1/users`
- `GET /api/v1/users/{id}`
- `POST /api/v1/users`
- `PUT /api/v1/users/{id}`
- ❌ No pot eliminar admins o super admins

🚙 VEHICLES
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`
- `POST /api/v1/vehicles`
- `PUT /api/v1/vehicles/{id}`
- `DELETE /api/v1/vehicles/{id}`
- `GET /api/v1/vehicles/{id}/reservations`
- `PATCH /api/v1/vehicles/{id}/location`

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
- `GET /api/v1/geofences/{id}/logs`
- `POST /api/v1/geofences/check-vehicle`
---

## 👑 Super Admin

👉 És l'administrador  de totes les Companyies (Tenants)

✔ Crear, modificar i eliminar administradors i super administradors.  
✔ Crear companyies.  
✔ Modificar companyies.  
✔ Eliminar companyies.


### Endpoints

👥 ADMINISTRADORS
- `GET /api/v1/admins`
- `GET /api/v1/admins/{id}`
- `POST /api/v1/admins`
- `PUT /api/v1/admins/{id}`
- `DELETE /api/v1/admins/{id}`

👥 SUPERADMINISTRADORS
- `GET /api/v1/superadmins`
- `GET /api/v1/superadmins/{id}`
- `POST /api/v1/superadmins`
- `PUT /api/v1/superadmins/{id}`
- `DELETE /api/v1/superadmins/{id}`

🏢 COMPANYIES
- `GET /api/v1/companies`
- `GET /api/v1/companies/{id}`
- `POST /api/v1/companies`
- `PUT /api/v1/companies/{id}`
- `DELETE /api/v1/companies/{id}`

---

# 📊 Matriu Resum de Permisos

### LLEGENDA  
✅ Total: Accés complet.  
👤 Parcial: Només sobre dades pròpies.  
❌ Cap: Sense accés.  

| Funcionalitat / Recurs           | Usuari  | Admin   | Super Admin |
|----------------------------------|:-------:|:-------:|:-----------:|
| Autenticació (Login/Register)    | ✅      | ✅       | ✅       |
| Veure Vehicles                   | ✅      | ✅       | ❌       |
| Gestionar Vehicles (CRUD)        | ❌      | ✅       | ❌       |
| Crear/Gestionar Reserves         | 👤      | ✅       | ❌       |
| Gestionar Tickets (Incidències)  | 👤      | ✅       | ❌       |
| Geofencing i Logs Operatius      | ❌      | ✅       | ❌       |
| Gestionar Usuaris (Clients)      | ❌      | ✅       | ❌       |
| Gestionar Administradors         | ❌      | ❌       | ✅       |
| Gestionar Super Administradors   | ❌      | ❌       | ✅       |
| Gestionar Companyies (Tenants)   | ❌      | ❌       | ✅       |

---


**Versió:** 1.0  
**Última actualització:** 26/02/2026

