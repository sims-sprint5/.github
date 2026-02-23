# Contributing to Blink

Guidelines for contributors to the Blink project (Frontend & Backend).

## Frontend

Vue 3 + Vite frontend project.

App available at `http://localhost:5173`

### Project Structure

```
src/
├── modules/          # Feature modules (auth, tickets, users, etc.)
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
- Avoid `any`; use `unknown` if needed
- Use Composition API with `<script setup>`

#### Styling

- Use Tailwind CSS (utility classes in templates)
- No inline styles
- Use `<style scoped>`

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
└── seeders/          # Database seeders
routes/
├── api.php           # API routes
├── web.php           # Web routes
└── console.php       # Console commands
resources/
├── views/            # Blade templates
├── css/              # Styles
└── js/               # JavaScript
```

### Naming Conventions

- **Models**: PascalCase singular (`User`, `Vehicle`, `Geofence`)
- **Controllers**: PascalCase with `Controller` suffix (`UserController`, `TicketController`)
- **Methods**: camelCase (`storeTicket`, `getUserReservations`)
- **Variables**: camelCase (`$userId`, `$vehicleData`)
- **Constants**: UPPER_SNAKE_CASE (`DB_TIMEOUT`, `API_RATE_LIMIT`)
- **Migrations**: snake_case with timestamp (`2026_01_21_create_users_table`)

### Code Standards

#### PHP & Laravel

- Follow PSR-12 coding standard
- Define types for all parameters and return types
- Use type hints; avoid mixing types
- Use dependency injection in controllers
- Implement middleware for cross-cutting concerns

#### Database

- Use migrations for schema changes
- Define relationships in Models
- Use scope methods for common queries
- Add proper indexes on frequently queried columns

## Git Workflow

### Branch Names

```
<type>/<issue>-<description>
```

Types: `feature/`, `bugfix/`, `docs/`, `refactor/`, `perf/`

Examples:
```bash
git checkout -b feature/BK-12
git checkout -b bugfix/456-fix-login
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

## Pull Request Process

### Before Submitting

1. Sync with main:
   ```bash
   git fetch origin
   git rebase origin/main
   ```

2. Test locally:
   ```bash
   npm run dev
   npm run build
   npm run preview
   ```

3. Self-review your changes

### Create PR

Use clear title following commit format:
```
feat(tickets): add filtering by status
fix(auth): resolve token refresh issue
```

Fill PR template:
- **Description**: What and why
- **Type**: Feature / Bug fix / Docs
- **Testing**: How to test
- **Screenshots**: If UI changes

## Best Practices

1. **Keep PRs focused** – one feature/fix per PR
2. **Small, logical commits** – easier to review and revert
3. **Import translations** – add keys to all locale files
4. **Handle errors** – try/catch, show toasts/messages
5. **Use composables** – reusable logic, not duplicated
6. **Test all states** – loading, empty, error, success
7. **Type everything** – avoid `any`