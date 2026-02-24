# Contribuint a Blink

Directrius per a col·laboradors del projecte Blink (Frontend & Backend).

---

## Frontend

Projecte frontend amb Vue 3 + Vite.

Aplicació disponible a `http://localhost:5173`

### Estructura del Projecte

```
src/
├── modules/          # Mòduls de funcionalitat (auth, tickets, users, etc.)
├── components/base/  # Components base reutilitzables
├── layouts/          # Components de maquetació
├── shared/           # Composables, services, types, utils
├── router/           # Definicions de rutes
└── locales/          # Traduccions i18n (en.json, es.json, ca.json)
```

Cada mòdul: `routes.ts`, `types/`, `services/`, `components/`, `views/`, `utils/`

### Convencions de Noms

- **Components**: PascalCase (`UserForm.vue`, `TicketTable.vue`)
- **Types**: PascalCase (`User`, `TicketFormData`)
- **Funcions/Variables**: camelCase (`validateEmail`, `fetchTickets`)
- **Constants**: UPPER_SNAKE_CASE (`API_TIMEOUT`, `MAX_RETRIES`)
- **Composables**: prefix `use` (`useToast`, `useSettings`)

### Estàndards de Codi

#### TypeScript & Vue

- Utilitzar TypeScript estricte (enforced a `tsconfig.json`)
- Definir tipus per a totes estructures de dades
- Evitar `any`; utilitzar `unknown` si és necessari
- Usar Composition API amb `<script setup>`

#### Estilitzat

- Usar Tailwind CSS (clases d'utilitat als templates)
- Sense estils inline
- Usar `<style scoped>`

## Backend

Projecte backend API Laravel.

API disponible a `http://localhost:8001`

### Estructura del Projecte

```
app/
├── Http/              # Controllers & Requests
├── Models/            # Eloquent Models
└── Providers/         # Service Providers
config/                # Fitxers de configuració
database/
├── migrations/        # Migracions de base de dades
├── factories/         # Model factories
└── seeders/          # Database seeders
routes/
├── api.php           # Rutes API
├── web.php           # Rutes web
└── console.php       # Comandes de consola
resources/
├── views/            # Plantilles Blade
├── css/              # Estils
└── js/               # JavaScript
```

### Convencions de Noms

- **Models**: PascalCase singular (`User`, `Vehicle`, `Geofence`)
- **Controllers**: PascalCase amb suffix `Controller` (`UserController`, `TicketController`)
- **Methods**: camelCase (`storeTicket`, `getUserReservations`)
- **Variables**: camelCase (`$userId`, `$vehicleData`)
- **Constants**: UPPER_SNAKE_CASE (`DB_TIMEOUT`, `API_RATE_LIMIT`)
- **Migrations**: snake_case amb timestamp (`2026_01_21_create_users_table`)

### Estàndards de Codi

#### PHP & Laravel

- Seguir l'estàndard de codificació PSR-12
- Definir tipus per a tots els paràmetres i tipus de retorn
- Usar type hints; evitar barrejar tipus
- Usar dependency injection als controllers
- Implementar middleware per a preocupacions transversals

#### Base de Dades

- Usar migracions per a canvis d'esquema
- Definir relacions als Models
- Usar scope methods per a consultes comunes
- Afegir índexs adequats a les columnes sovint consultades

## Flux de Treball Git

### Noms de Branques

```
<type>/<issue>-<description>
```

Tipus: `feature/`, `bugfix/`, `docs/`, `refactor/`, `perf/`

Exemples:
```bash
git checkout -b feature/BK-12
git checkout -b bugfix/456-fix-login
```

### Missatges de Commit

```
<type>(<scope>): <message>
```

Tipus: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`

Exemples:
```bash
git commit -m "feat(tickets): add filtering by status"
git commit -m "fix(auth): resolve token expiration"
```

## Procés de Pull Request

### Abans de Presentar

1. Sincronitzar amb main:
   ```bash
   git fetch origin
   git rebase origin/main
   ```

2. Provar localment:
   ```bash
   npm run dev
   npm run build
   npm run preview
   ```

3. Revisar els canvis

### Crear PR

Usar un títol clar seguint el format de commit:
```
feat(tickets): add filtering by status
fix(auth): resolve token refresh issue
```

Omplir la plantilla de PR:
- **Descripció**: Què i per què
- **Tipus**: Feature / Correccio de bug / Docs
- **Prova**: Com provar
- **Capturas**: Si és necessari canvis a la UI

## Millors Pràctiques

1. **Mantenir PRs enfocats** – una funcionalitat/correció per PR
2. **Commits petits i lògics** – més fàcils de revisar i revertir
3. **Importar traduccions** – afegir claus a tots els fitxers de locale
4. **Gestionar errors** – try/catch, mostrar toasts/missatges
5. **Usar composables** – lògica reutilitzable, no duplicada
6. **Provar tots els estats** – loading, buit, error, éxito
7. **Tipar tot** – evitar `any`

## Altres

Si realitzem alguna refactorització o canvi documentar-ho i explicar perquè.
Si ens trobem algun error documentar-lo i documentar com ho hem sol·lucionat.