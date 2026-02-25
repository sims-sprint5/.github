# 🔐 Rols i Permisos del Sistema

Aquest document defineix els rols del sistema i els permisos associats a cada un.

---

# 👤 Usuari

Rol orientat a client final de l’aplicació.

## 🔑 Autenticació
- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/logout`
- `GET /api/v1/auth/me`
- `POST /api/v1/auth/change-password`

## 🚙 Vehicles
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`

## 📅 Reserves
- `GET /api/v1/reservations` *(només pròpies)*
- `GET /api/v1/reservations/{id}` *(només pròpia)*
- `POST /api/v1/reservations`
- `PUT /api/v1/reservations/{id}` *(només pròpia)*
- `DELETE /api/v1/reservations/{id}` *(cancel·lar pròpia)*
- `GET /api/v1/reservations/user/{userId}` *(només el seu ID)*

## 🎫 Tickets
- `GET /api/v1/tickets` *(només propis)*
- `GET /api/v1/tickets/{id}` *(només propi)*
- `POST /api/v1/tickets`

---

# 🛠️ Admin

Inclou tots els permisos d’**Usuari** més funcionalitats de gestió.

## 👥 Usuaris
- `GET /api/v1/users`
- `GET /api/v1/users/{id}`
- `POST /api/v1/users`
- `PUT /api/v1/users/{id}`
- ❌ No pot eliminar admins o super admins

## 🚙 Vehicles
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`
- `POST /api/v1/vehicles`
- `PUT /api/v1/vehicles/{id}`
- `DELETE /api/v1/vehicles/{id}`
- `GET /api/v1/vehicles/{id}/reservations`
- `PATCH /api/v1/vehicles/{id}/location`

## 📅 Reserves
- `GET /api/v1/reservations`
- `GET /api/v1/reservations/{id}`
- `GET /api/v1/reservations/user/{userId}`
- `PATCH /api/v1/reservations/{id}/status`

## 🎫 Tickets
- `GET /api/v1/tickets`
- `GET /api/v1/tickets/{id}`
- `PUT /api/v1/tickets/{id}`
- `DELETE /api/v1/tickets/{id}`
- `PATCH /api/v1/tickets/{id}/assign`
- `PATCH /api/v1/tickets/{id}/status`

## 🗺️ Geofencing
- `GET /api/v1/geofences`
- `GET /api/v1/geofences/{id}`
- `GET /api/v1/geofences/{id}/logs`
- `POST /api/v1/geofences/check-vehicle`

- ## 🔐 Control del Sistema
- Consultar logs del sistema
- Bloquejar comptes d’Usuaris (no Admins ni Super Admins)
- Reset de contrasenya d’Usuaris
- Veure mètriques operatives (vehicles actius, reserves actives, tickets oberts etc.)
- Forçar canvi d’estat de reserves
- Reassignar vehicles si hi ha incidències

---

# 👑 Super Admin

Inclou tots els permisos d’**Admin** més control total del sistema.

## 👥 Control Total d’Administradors

- `DELETE /api/v1/admin/{id}`
- `DELETE /api/v1/superadmin/{id}`

- Pot crear Admins i Super Admins
  
## 🔐 Control del Sistema
- Configuracions globals
- Auditoria / logs del sistema
  
---

# 📊 Matriu Resum de Permisos

| Funcionalitat              | Usuari | Admin   | Super Admin |
|----------------------------|--------|---------|-------------|
| Autenticació               | ✅     | ✅      | ✅          |
| Crear reserves             | ✅     | ✅      | ✅          |
| Gestionar vehicles         | ❌     | ✅      | ✅          |
| Gestionar usuaris          | ❌     | Parcial | Total       |
| Assignar tickets           | ❌     | ✅      | ✅          |
| Gestionar geofences        | ❌     | Lectura | Total       |
| Eliminar usuaris           | ❌     | ❌      | ✅          |

---


**Versió:** 1.0  
**Última actualització:** 2026
