# Backend Libraries

List of all libraries used in the project, their version and purpose.

---

## 📦 Production Dependencies (PHP/Composer)

| Library | Version | Description |
|-----------|--------|------------|
| **PHP** | ^8.2 | Base programming language of the project |
| **laravel/framework** | ^12.0 | Main framework. Provides routing, ORM (Eloquent), migrations, validation, middleware, queues, events and the full MVC architecture |
| **laravel/sanctum** | ^4.0 | Token-based API authentication. Generates and manages personal access tokens to protect REST API endpoints |
| **laravel/tinker** | ^2.10.1 | REPL (interactive console) for Laravel. Allows running PHP code directly against the application for debugging and quick testing |

---

## 🧩 Spatie (Laravel)

### 🔐 spatie/laravel-permission — Role and permission management
Allows defining roles and permissions flexibly and assigning them to users. Simplifies access control for routes and system functionalities, and integrates with Laravel middleware and policies.

- Role and permission assignment to users.
- Access validation for routes and system actions.
- Integration with guards and middleware.
- Clear, robust and easily extendable API.

Used to manage access based on user type (User, Admin, Super Admin) and ensure they can only perform the actions assigned to them.

---

## 🛠️ Development Dependencies (PHP/Composer)

| Library | Version | Description |
|-----------|--------|------------|
| **fakerphp/faker** | ^1.23 | Fake data generator (names, emails, addresses, etc.). Used in factories and seeders to populate the database with test data |
| **laravel/pail** | ^1.2.2 | Real-time log viewer from the terminal. Displays application logs with colors and filters |
| **laravel/pint** | ^1.24 | PHP code formatter. Automatically applies Laravel's code style rules (based on PHP-CS-Fixer) |
| **laravel/sail** | ^1.41 | Lightweight Docker environment for Laravel development. Provides simplified commands to manage containers |
| **mockery/mockery** | ^1.6 | Mocking library for tests. Allows simulating dependencies and verifying behaviours in unit tests |
| **nunomaduro/collision** | ^8.6 | Improves error display in the console. Shows readable, coloured stack traces when a test or command fails |
| **phpunit/phpunit** | ^11.5.3 | Unit and integration testing framework. Runs project tests with `php artisan test` |

---

## 🌐 Frontend Dependencies (NPM)

| Library | Version | Description |
|-----------|--------|------------|
| **vite** | ^7.0.7 | Ultra-fast bundler and development server. Compiles and serves frontend assets (CSS, JS) |
| **laravel-vite-plugin** | ^2.0.0 | Plugin that integrates Vite with Laravel. Manages asset loading in Blade views |
| **tailwindcss** | ^4.0.0 | Utility-first CSS framework. Provides predefined CSS classes for rapid design |
| **@tailwindcss/vite** | ^4.0.0 | TailwindCSS plugin for Vite. Processes and optimises Tailwind classes during build |
| **axios** | ^1.11.0 | HTTP client for JavaScript. Makes AJAX requests from the frontend to the API |
| **concurrently** | ^9.0.1 | Runs multiple commands in parallel. Used in the `dev` script to launch the server, queues, logs and Vite simultaneously |

---

## 🐳 Docker Services

| Service | Image | Description |
|--------|--------|------------|
| **PostgreSQL + PostGIS** | `postgis/postgis:15-3.3` | Relational database with geospatial extension for geofencing |
| **pgAdmin 4** | `dpage/pgadmin4` | Web interface to administer and visualise the PostgreSQL database |
| **Laravel API** | `php:8.4-fpm` | Container with PHP-FPM that runs the Laravel application |

---

## 🔗 Relationship Between Libraries

```text
Laravel Framework (core)
├── Sanctum → API Authentication (tokens)
├── Tinker → Interactive debugging
├── Eloquent ORM → Models and migrations → PostgreSQL
└── Artisan CLI → Commands (migrate, seed, test...)

Testing
├── PHPUnit → Unit/feature tests
├── Mockery → Dependency mocking
├── Collision → Visual errors in the console
└── Faker → Test data for seeders/factories

Code Quality
└── Pint → Automatic formatting (CI/CD)

Frontend (Assets)
├── Vite + Laravel Plugin → Compilation
├── TailwindCSS → Styles
└── Axios → HTTP requests
```

Last updated: February 26, 2026
