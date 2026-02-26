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

---

## 👑 Super Admin

👉 És el màxim nivell del sistema.

✔ Pot fer tot el que fa un admin.  
✔ Crear administradors i super administradors.  
✔ Eliminar administradors o super administradors.  
✔ Gestionar configuracions globals.  
✔ Accedir a auditoria i logs complets.

---
