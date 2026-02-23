# Code Conventions

## Frontend - Vue 3

### Module Structure

When creating a new Vue module, organize it as follows:

```
src/modules/mymodule/
├── routes.ts
├── components/
├── views/
├── services/
├── composables/
├── types/
└── utils/
```

### Naming Conventions

- **Components**: PascalCase (`UserForm.vue`, `BaseButton.vue`)
- **Composables**: `use` prefix (`useToast.ts`, `useFetch.ts`)
- **Services**: camelCase + `.service.ts` (`user.service.ts`)
- **Types**: PascalCase (`User.ts`, `TicketFormData.ts`)
- **Utils**: camelCase (`validators.ts`)
- **Stores**: camelCase + `Store` (`userStore.ts`)
- **Folders**: lowercase (`modules/auth/`)
- **Constants**: UPPER_SNAKE_CASE (`API_TIMEOUT`)

### Code Style

- Always use `<script setup>` syntax
- Define Props and Emits with TypeScript interfaces
- Use `ref` for local state, `computed` for derived values
- Use Tailwind CSS, no inline styles
- Use `<style scoped>` block
- Always use TypeScript, avoid `any`
- Implement try-catch for error handling

---

## Backend - Laravel

### Module Structure

```
app/
├── Models/
├── Http/Controllers/
├── Http/Requests/
routes/api.php
database/migrations/
```

### Naming Conventions

- **Models**: PascalCase singular (`User`, `Vehicle`)
- **Controllers**: PascalCase + `Controller` (`UserController`)
- **Methods**: RESTful verbs (`index`, `show`, `store`, `update`, `destroy`)
- **Requests**: `Store/Update` + Model (`StoreUserRequest`)
- **Routes**: snake_case (`/api/users`, `/api/tickets/{id}`)
- **Tables**: snake_case plural (`users`, `tickets`)
- **Columns**: snake_case (`user_id`, `is_active`)
- **Foreign Keys**: snake_case + `_id` (`user_id`)
- **Boolean**: `is_` or `has_` prefix (`is_active`)
- **Migrations**: timestamp + snake_case (`2026_01_21_create_users_table`)

### Code Style

- Always add type hints and return types
- Use form requests for validation
- Use Eloquent relationships with proper types
- Follow PSR-12 standard (4-space indentation)
- Use dependency injection in constructors
- Return proper HTTP status codes (201 create, 204 delete)
- Use null-safe operator `?->` for null handling