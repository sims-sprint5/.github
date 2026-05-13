# Contributing to FLEETLY

Guidelines for contributors to the Blink project (Frontend & Backend).

---
WEBS:

LANDINGPAGE:

https://jilsoftwares.deltahost.asix2.iesmontsia.cat/

SUPERADMIN:

https://jilsoftwares.deltahost.asix2.iesmontsia.cat/login

TENANT:

https://prova.jilsoftwares.deltahost.asix2.iesmontsia.cat/admin/reservations

## Frontend

Frontend project with Vue 3 + Vite.

Application available at `http://localhost:5173`

### Project Structure

```
src/
├── modules/          # Functionality modules (auth, tickets, users, etc.)
├── components/base/  # Reusable base components
├── layouts/          # Layout components
├── shared/           # Composables, services, types, utils
├── router/           # Route definitions
└── locales/          # i18n translations (en.json, es.json, ca.json)
```

Each module: `routes.ts`, `types/`, `services/`, `components/`, `views/`, `utils/`

### Naming Conventions

- **Components**: PascalCase (`UserForm.vue`, `TicketTable.vue`)
- **Types**: PascalCase (`User`, `TicketFormData`)
- **Functions/Variables**: camelCase (`validateEmail`, `fetchTickets`)
- **Constants**: UPPER_SNAKE_CASE (`API_TIMEOUT`, `MAX_RETRIES`)
- **Composables**: `use` prefix (`useToast`, `useSettings`)

### Code Standards

#### TypeScript & Vue

- Use strict TypeScript (enforced in `tsconfig.json`)
- Define types for all data structures
- Avoid `any`; use `unknown` if necessary
- Use Composition API with `<script setup>`

#### Styling

- Use Tailwind CSS (utility classes in templates)
- No inline styles
- Use `<style scoped>`

---

## Backend

Laravel API backend project.

API available at `http://localhost:8001`

### Project Structure

```
app/
├── Http/              # Controllers & Requests
├── Models/            # Eloquent Models
└── Providers/         # Service Providers
config/                # Configuration files
database/
├── migrations/        # Database migrations
├── factories/         # Model factories
└── seeders/           # Database seeders
routes/
├── api.php            # API routes
├── web.php            # Web routes
└── console.php        # Console commands
resources/
├── views/             # Blade templates
├── css/               # Styles
└── js/                # JavaScript
```

### Naming Conventions

- **Models**: Singular PascalCase (`User`, `Vehicle`, `Geofence`)
- **Controllers**: PascalCase with `Controller` suffix (`UserController`, `TicketController`)
- **Methods**: camelCase (`storeTicket`, `getUserReservations`)
- **Variables**: camelCase (`$userId`, `$vehicleData`)
- **Constants**: UPPER_SNAKE_CASE (`DB_TIMEOUT`, `API_RATE_LIMIT`)
- **Migrations**: snake_case with timestamp (`2026_01_21_create_users_table`)

### Code Standards

#### PHP & Laravel

- Follow the PSR-12 coding standard
- Define types for all parameters and return types
- Use type hints; avoid mixing types
- Use dependency injection in controllers
- Implement middleware for cross-cutting concerns

#### Database

- Use migrations for schema changes
- Define relationships in Models
- Use scope methods for common queries
- Add appropriate indexes to frequently queried columns

---

## Git Workflow

---

## 📌 Process for Creating a Branch

In this project, **every new feature, fix or improvement must be developed on an independent branch**.

Working directly on `develop` is not allowed.

This ensures:

- Stability of the `develop` branch
- Parallel work without conflicts
- Traceability of every change
- Controlled code review through Pull Requests

---

### 1️⃣ Before Creating the Branch

Before creating a new branch:

- An **associated identifier must exist** (e.g. `BK-12`)
- The description of the change must be clear
- The branch must correspond to **a single feature or fix**

---

### 2️⃣ Update `develop`

Always start from the latest stable version of the project.

```bash
git checkout develop
git pull origin develop
```

### Commit Messages

```
<type>(<scope>): <message>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`

Examples:
```bash
git commit -m "feat(tickets): add filtering by status"
git commit -m "fix(auth): resolve token expiration"
```

---

## Pull Request Process

---

## 1️⃣ Preparation before creating the PR

Before creating a PR, **make sure all functionalities affected by your changes work correctly**. This prevents errors in `develop` and makes the review easier.

### 1.1 Sync with develop

Update your branch with the latest version of `develop`:

```bash
git pull (inside the develop branch) or git fetch origin develop (if the branch is not found)
```

### 2.1 Pull Request Title

The PR title is very important: it must be clear, descriptive and follow a consistent format to make reading and reviewing easier.

- `<type>`: type of change
- `<scope>`: affected area or module
- `<message>`: brief explanation of the change

---

## Allowed types

- `feat` → new feature
- `fix` → bug fix
- `refactor` → code refactoring
- `docs` → documentation
- `style` → style or formatting changes (non-functional)
- `perf` → performance improvements
- `test` → adding/modifying tests
- `chore` → general tasks (configs, scripts, etc.)

---

## Examples of correct titles

```
feat(tickets): add filtering by status
fix(auth): resolve token refresh issue
refactor(users): extract validation logic
docs(readme): update installation instructions
```

---

## Other

If we carry out any refactoring or change, document it and explain why.
If we encounter any error, document it and document how we solved it.

---

## 📘 Code Convention for Classes

Project classes must follow these rules to maintain clean, readable and scalable code.

### ✅ Class Name Format

- Use **PascalCase**
- The name must be **descriptive** and clearly represent the responsibility of the class
- Avoid non-standard abbreviations

Correct examples:
- UserController
- VehicleService
- TicketRepository
- GeofenceValidator

---

## When to merge into the main branch

Once the Alpha is ready on the `develop` branch, we can proceed with the merge into `main`.
