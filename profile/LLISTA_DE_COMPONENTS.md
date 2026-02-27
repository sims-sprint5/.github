# Llistat de components

El projecte està organitzat seguint una arquitectura modular i escalable, diferenciant clarament entre components generals reutilitzables i components específics per mòdul.

## Components base (globals)

- `BaseButton` — Botó reutilizable amb diferents estils - src/components/base/BaseButton.vue  
- `BaseInput` — Camp d'entrada amb validació i format - src/components/base/BaseInput.vue  
- `BaseCard` — Contenidor per agrupar contingut - src/components/base/BaseCard.vue  
- `BaseToast` — Notificació temporal en pantalla - src/components/base/BaseToast.vue  
- `BaseModal` — Finestra modal reutilitzable - src/components/base/BaseModal.vue  
- `BaseTable` — Taula genèrica amb paginació i ordenació - src/components/base/BaseTable.vue  

## Layouts

- `AppLayout` — Estructura principal de l'aplicació - src/layouts/AppLayout.vue  
- `Navbar` — Barra de navegació superior - src/layouts/components/Navbar.vue  
- `Sidebar` — Menú lateral de navegació - src/layouts/components/Sidebar.vue  
- `LanguageSelector` — Selector d'idioma de la UI - src/components/LanguageSelector.vue  

## Mòdul Auth

- `AuthLogo` — Logo per a pantalles d'autenticació (login, register) - src/modules/auth/components/AuthLogo.vue  
- `AuthBackground` — Fons visual per a pàgines d'autenticació - src/modules/auth/components/AuthBackground.vue  

## Mòdul Tickets

- `TicketForm` — Formulari per crear/editar un ticket - src/modules/tickets/components/TicketForm.vue  
- `TicketTable` — Llista de tickets per a usuaris - src/modules/tickets/components/TicketTable.vue  
- `AdminTicketTable` — Taula de tickets amb opcions administratives - src/modules/tickets/components/AdminTicketTable.vue  
- `TicketChat` — Xat intern associat a un ticket - src/modules/tickets/components/TicketChat.vue  
- `UserTicketChat` — Interfície de xat per a usuaris finals - src/modules/tickets/components/UserTicketChat.vue  

## Mòdul Users

- `UserTable` — Taula d'usuaris amb accions - src/modules/users/components/UserTable.vue  
- `UserForm` — Formulari per crear/editar usuaris - src/modules/users/components/UserForm.vue  
