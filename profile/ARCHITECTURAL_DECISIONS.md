# Architectural Decisions

## Frontend

- **Vue 3** with Composition API and `<script setup>` syntax
- **Vite** as build tool (fast HMR, native ES modules in dev)
- **TypeScript** for type safety
- **Tailwind CSS** for utility-first styling
- **Vue Router** for page routing
- **Vue i18n** for multi-language support (Catalan, Spanish, English)
- **Axios** for HTTP requests
- **Heroicons** for icon components
- **Feature-based structure**: Each module (auth, users, dashboard, tickets, settings) contains its own components, views, services, types, and utils

## Backend

- **PHP 8.2+** for type safety and modern syntax
- **Laravel 12** as the framework
- **Laravel Sanctum** for token-based API authentication
- **Eloquent ORM** for database operations with eloquent relationships
- **API Controllers** structured under `app/Http/Controllers/Api/`
- **Database Migrations** for schema versioning and reproducibility
- **Models**: User, Vehicle, Reservation, Ticket, Geofence, VehicleGeofenceLog
- **Docker** for containerized development and deployment
- **MariaDB** as the relational database
- **PHPUnit** for automated testing
- **Token-based authentication** via Sanctum

## General

- **Separate repositories**: Frontend and backend in independent repositories for clear separation
- **Modular, domain-driven design** for maintainability
- **Environment configuration** via `.env` files
- **Local development** with consistent tooling across team