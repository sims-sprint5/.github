# Llistat de components

## Vistes (pàgines)

### Mòdul Auth
- **LoginView** — Pàgina d'inici de sessió — `LoginView.vue`
- **RegisterView** — Pàgina de registre — `RegisterView.vue`

### Mòdul Mapa
- **MapView** — Mapa administratiu amb geofences i vehicles — `MapView.vue`
- **UserMapView** — Mapa per a usuari final — `UserMapView.vue`
- **ReservationPage** *(mapa)* — Pàgina de reserva des del mapa — `mapa/views/ReservationPage.vue`

### Mòdul Reserves
- **ReservationPage** — Pàgina principal de reserves (cerca i llistat de vehicles) — `reservations/views/ReservationPage.vue`
- **MyReservationsView** — Les meves reserves — `MyReservationsView.vue`
- **AdminReservationsView** — Gestió de totes les reserves (admin) — `AdminReservationsView.vue`
- **ReservationCompletedView** — Confirmació post-pagament Stripe — `ReservationCompletedView.vue`

### Mòdul Vehicles
- **VehiclesView** — Gestió de vehicles (admin) — `VehiclesView.vue`

### Mòdul Usuaris
- **UsersView** — Gestió d'usuaris (admin) — `UsersView.vue`

### Mòdul Tickets
- **TicketsView** — Els meus tickets — `TicketsView.vue`
- **AdminTicketsView** — Gestió de tickets (admin) — `AdminTicketsView.vue`
- **ChatView** — Vista de xat en temps real — `ChatView.vue`

### Mòdul Superadmin
- **SuperadminLoginView** — Login del superadmin — `SuperadminLoginView.vue`
- **SuperadminDashboardView** — Panell principal del superadmin — `SuperadminDashboardView.vue`
- **SuperadminsView** — Gestió de superadmins — `SuperadminsView.vue`
- **TenantsView** — Gestió de tenants/companyies — `TenantsView.vue`

### Mòdul Settings
- **SettingsView** — Configuració del compte d'usuari — `SettingsView.vue`

### Compartit
- **UnauthorizedView** — Pàgina 403 (accés denegat) — `UnauthorizedView.vue`

---

## Components base (globals)
- **BaseButton** — Botó reutilitzable amb variants — `BaseButton.vue`
- **BaseInput** — Camp d'entrada reutilitzable (admet validació/errors) — `BaseInput.vue`
- **BaseCard** — Contenidor tipus targeta — `BaseCard.vue`
- **BaseToast** — Notificacions tipus toast — `BaseToast.vue`
- **BaseModal** — Modal reutilitzable — `BaseModal.vue`
- **BaseTable** — Taula genèrica — `BaseTable.vue`
- **BasePagination** — Paginació reutilitzable — `BasePagination.vue`
- **BaseTooltip** — Tooltip reutilitzable — `BaseTooltip.vue`
- **BaseDateTimePicker** — Selector de data i hora (Flatpickr) — `BaseDateTimePicker.vue`
- **ResponsiveTable** — Taula amb suport responsive — `ResponsiveTable.vue`
- **ChatBot** — Chatbot global de l'aplicació — `ChatBot.vue`
- **LanguageSelector** — Selector d'idioma — `LanguageSelector.vue`

---

## Layouts
- **AppLayout** — Estructura principal (Navbar + Sidebar) — `AppLayout.vue`
- **Navbar** — Barra superior — `Navbar.vue`
- **Sidebar** — Menú lateral — `Sidebar.vue`

---

## Mòdul Auth
- **AuthLogo** — Logotip per a login/registre — `AuthLogo.vue`
- **AuthBackground** — Fons visual d'autenticació — `AuthBackground.vue`

---

## Mòdul Tickets
- **TicketForm** — Formulari de creació de ticket — `TicketForm.vue`
- **TicketTable** — Taula/llistat de tickets de l'usuari — `TicketTable.vue`
- **AdminTicketTable** — Taula de tickets (admin) — `AdminTicketTable.vue`
- **TicketChat** — Xat associat a un ticket (admin) — `TicketChat.vue`
- **UserTicketChat** — Xat per a usuari final — `UserTicketChat.vue`

---

## Mòdul Usuaris
- **UserTable** — Taula d'usuaris — `UserTable.vue`
- **UserForm** — Formulari d'usuari (creació/edició) — `UserForm.vue`

---

## Mòdul Mapa
- **MapContainer** — Contenidor del mapa Leaflet — `MapContainer.vue`
- **GeofenceTable** — Taula de geocercas — `GeofenceTable.vue`
- **GeofenceFormModal** — Modal d'alta/edició de geocerca — `GeofenceFormModal.vue`
- **GeofenceLogsModal** — Modal de logs de geocerca — `GeofenceLogsModal.vue`
- **VehicleDetailsModal** — Modal de detalls de vehicle al mapa — `VehicleDetailsModal.vue`
- **CustomModal** — Modal personalitzat del mòdul mapa — `CustomModal.vue`

---

## Mòdul Reserves
- **FilterSidebar** — Panell lateral de filtres de cerca — `FilterSidebar.vue`
- **VehicleList** — Llistat de vehicles amb paginació — `VehicleList.vue`
- **VehicleCard** — Targeta de vehicle amb acció de reservar — `VehicleCard.vue`
- **VehicleDetailModal** — Modal de detalls del vehicle — `VehicleDetailModal.vue`
- **BusyDaysCalendar** — Calendari de dies ocupats del vehicle — `BusyDaysCalendar.vue`
- **ReserveAndPayButton** — Botó per reservar i pagar via Stripe — `ReserveAndPayButton.vue`
- **MyReservationsTable** — Taula de reserves de l'usuari — `MyReservationsTable.vue`
- **EditReservationModal** — Modal d'edició de reserva — `EditReservationModal.vue`
- **ReservationRenewalModal** — Modal de renovació de reserva (Stripe) — `ReservationRenewalModal.vue`
- **ReservationLogTable** — Taula de logs/historial de reserves (admin) — `ReservationLogTable.vue`

---

## Mòdul Vehicles
- **VehicleTable** — Taula de vehicles — `VehicleTable.vue`
- **VehicleForm** — Formulari de vehicle (creació/edició) — `VehicleForm.vue`

---

## Mòdul Superadmin
- **TenantForm** — Formulari per crear/editar tenants — `TenantForm.vue`
