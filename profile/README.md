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

---

## 📌 Procés per Crear una Branca

En aquest projecte, **cada nova funcionalitat, correcció o millora s’ha de desenvolupar en una branca independent**.  

No es permet treballar directament sobre `develop`.

Això garanteix:

- Estabilitat de la branca `develop`
- Treball en paral·lel sense conflictes
- Traçabilitat de cada canvi
- Revisió de codi controlada mitjançant Pull Requests

---

### 1️⃣ Abans de Crear la Branca

Abans de crear una branca nova:

- Ha d’existir un **identificador associat** (ex: `BK-12`)
- S’ha de tenir clara la descripció del canvi
- La branca ha de correspondre a **una sola funcionalitat o correcció**

---

### 2️⃣ Actualitzar `develop`

Sempre s’ha de partir de l’última versió estable del projecte.

```bash
git checkout develop
git pull origin develop
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

---

# Pull Request Process
    
---

## 1️⃣ Preparació abans de crear la PR

Abans de crear una PR, **assegura’t que totes les funcionalitats que afecten els teus canvis funcionen correctament**. Això evita errors en `develop` i facilita la revisió.

### 1.1 Sincronitzar amb develop

Actualitza la teva branca amb la darrera versió de `develop`:

```bash
git pull (dins de la branca develop) o git fetch origin develop (si no troba la branca del git)

### 2.1 Títol de la Pull Request

El títol de la PR és molt important: ha de ser clar, descriptiu i seguir un format uniforme per facilitar la lectura i revisió.

- `<type>`: tipus del canvi
- `<scope>`: àrea o mòdul afectat
- `<missatge>`: explicació breu del canvi

```

---

## Tipus permesos

- `feat` → nova funcionalitat
- `fix` → correcció de bug
- `refactor` → refactorització de codi
- `docs` → documentació
- `style` → canvis d’estil o format (no funcional)
- `perf` → millores de rendiment
- `test` → afegir/modificar tests
- `chore` → tasques generals (configs, scripts, etc.)

---

## Exemples de títols correctes
feat(tickets): add filtering by status
fix(auth): resolve token refresh issue
refactor(users): extract validation logic
docs(readme): update installation instructions
---

## Format obligatori


## Altres

Si realitzem alguna refactorització o canvi documentar-ho i explicar perquè.
Si ens trobem algun error documentar-lo i documentar com ho hem sol·lucionat.

## 📘 Convenció de Codi per a Classes

Les classes del projecte han de seguir les següents normes per mantenir un codi net, llegible i escalable.

### ✅ Format dels Noms de Classes

- Utilitzar **PascalCase**
- El nom ha de ser **descriptiu** i representar clarament la responsabilitat de la classe
- Evitar abreviacions no estàndard

Exemples correctes:
- UserController
- VehicleService
- TicketRepository
- GeofenceValidator

---

# Quan fer merge amb la branca main

Quan l'Alpha estigui preparada a la branca develop ja podrem fer el merge cap a la branca main.
