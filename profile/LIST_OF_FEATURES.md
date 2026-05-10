# 🔐 System Functionalities – Simple Explanation

This document explains **in a very simple way** what each type of user can do within the system.

---

## 👤 User

👉 This is the regular client of the application.

✔ Can register, log in and change their password.
✔ Can update their profile.
✔ Can view available vehicles and their occupancy calendar.
✔ Can create reservations and pay via Stripe.
✔ Can renew their reservations via Stripe.
✔ Can view, modify or cancel **only their own reservations**.
✔ Can view and create their own tickets.
✔ Can send and receive messages in their tickets.
✔ Can view geofences on the map.
✔ Can use the chatbot.

❌ Cannot manage other users or the system.

### Endpoints

🔑 AUTHENTICATION
- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/logout`
- `POST /api/v1/auth/logout-all` *(close all sessions)*
- `GET /api/v1/auth/me`
- `PATCH /api/v1/auth/me` *(update profile)*
- `POST /api/v1/auth/change-password`

🚙 VEHICLES
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`
- `GET /api/v1/vehicles-calendar` *(vehicles with occupancy calendar)*
- `GET /api/v1/disponibilitat/{id}` *(availability of a vehicle)*

📅 RESERVATIONS
- `GET /api/v1/reservations` *(own only)*
- `GET /api/v1/reservations/{id}` *(own only)*
- `POST /api/v1/reservations`
- `PUT /api/v1/reservations/{id}` *(own only)*
- `DELETE /api/v1/reservations/{id}` *(cancel own)*
- `GET /api/v1/reservations/user/{userId}` *(own ID only)*
- `GET /api/v1/reservations/check-availability` *(check availability)*
- `POST /api/v1/reservations/checkout` *(initiate Stripe payment)*
- `POST /api/v1/reservations/{id}/renewal-intent` *(renew reservation via Stripe)*

🎫 TICKETS
- `GET /api/v1/tickets` *(own only)*
- `GET /api/v1/tickets/{id}` *(own only)*
- `POST /api/v1/tickets`
- `GET /api/v1/tickets/user/{userId}` *(their tickets)*
- `GET /api/v1/tickets/{ticket}/messages` *(message history)*
- `POST /api/v1/tickets/{ticket}/messages` *(send message)*

🗺️ GEOFENCES *(read only)*
- `GET /api/v1/geofences`
- `GET /api/v1/geofences/{id}`

🤖 CHATBOT
- `POST /api/v1/chat/ask`

💳 STRIPE PAYMENTS
- `POST /api/v1/reservations/checkout` *(initiate payment for a new reservation)*
- `POST /api/v1/reservations/{id}/renewal-intent` *(initiate payment to renew a reservation)*

---

## 🛠️ Admin

👉 This is the system manager.

Has everything a user can do, plus:

### 👥 Users
✔ View, create, modify and delete users.
❌ Cannot delete admins or super admins.

### 🚙 Vehicles
✔ Create, modify and delete vehicles.
✔ View reservations for a vehicle.
✔ Update vehicle location.
✔ Sync availability for all vehicles.

### 📅 Reservations
✔ View all reservations.
✔ Change the status of a reservation.

### 🎫 Tickets
✔ Manage tickets:
- Modify and delete.
- Assign them.
- Change status.

### 🌍 Geofencing
✔ Create, modify and delete geographic zones.
✔ Review vehicle logs within zones.
✔ Check whether a vehicle is inside a zone.

### Endpoints

👥 USERS
- `GET /api/v1/users`
- `GET /api/v1/users/{id}`
- `POST /api/v1/users`
- `PUT /api/v1/users/{id}`
- `DELETE /api/v1/users/{id}` *(cannot delete admins or super admins)*

🚙 VEHICLES
- `GET /api/v1/vehicles`
- `GET /api/v1/vehicles/{id}`
- `POST /api/v1/vehicles`
- `PUT /api/v1/vehicles/{id}`
- `DELETE /api/v1/vehicles/{id}`
- `GET /api/v1/vehicles/{id}/reservations`
- `PATCH /api/v1/vehicles/{id}/location`
- `POST /api/v1/vehicles/sync-all-availability`

📅 RESERVATIONS
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

👉 This is the administrator of all Companies (Tenants).

✔ Log in to their own panel.
✔ Create and delete super admins.
✔ Create, modify and delete companies (tenants).

### Endpoints

🔑 SUPERADMIN AUTHENTICATION
- `POST /api/v1/superadmin/auth/login`
- `GET /api/v1/superadmin/auth/me`
- `POST /api/v1/superadmin/auth/logout`

👥 SUPER ADMINS
- `GET /api/v1/superadmin/admins`
- `POST /api/v1/superadmin/admins`
- `DELETE /api/v1/superadmin/admins/{id}`

🏢 COMPANIES (TENANTS)
- `GET /api/v1/superadmin/tenants`
- `GET /api/v1/superadmin/tenants/{id}`
- `POST /api/v1/superadmin/tenants`
- `PUT /api/v1/superadmin/tenants/{id}`
- `DELETE /api/v1/superadmin/tenants/{id}`

---

## 💳 Stripe Webhooks

Handled internally by the system:
- `POST /api/v1/stripe/webhook` *(payment and renewal confirmations)*

---

# 📊 Permissions Summary Matrix

### LEGEND
✅ Full: Complete access.
👤 Partial: Own data only.
❌ None: No access.

| Feature / Resource                  | User   | Admin | Super Admin |
|-------------------------------------|:------:|:-----:|:-----------:|
| Authentication (Login/Register)     | ✅     | ✅    | ✅          |
| Update profile                      | ✅     | ✅    | ❌          |
| View Vehicles                       | ✅     | ✅    | ❌          |
| Manage Vehicles (CRUD)              | ❌     | ✅    | ❌          |
| Create Reservations & Pay (Stripe)  | 👤     | ✅    | ❌          |
| Renew Reservations (Stripe)         | 👤     | ✅    | ❌          |
| Manage Tickets (Incidents)          | 👤     | ✅    | ❌          |
| Ticket Messaging                    | 👤     | ✅    | ❌          |
| View Geofences (map)                | ✅     | ✅    | ❌          |
| Manage Geofences (CRUD + logs)      | ❌     | ✅    | ❌          |
| Chatbot                             | ✅     | ✅    | ❌          |
| Manage Users (Clients)              | ❌     | ✅    | ❌          |
| Manage Super Admins                 | ❌     | ❌    | ✅          |
| Manage Companies (Tenants)          | ❌     | ❌    | ✅          |

---

**Version:** 2.1
**Last updated:** 08/05/2026
