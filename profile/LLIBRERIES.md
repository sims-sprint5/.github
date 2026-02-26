# Llibreries del Backend

Llistat de totes les llibreries utilitzades en el projecte, la seva versió i propòsit.

---

## 📦 Dependències de Producció (PHP/Composer)

| Llibreria | Versió | Descripció |
|-----------|--------|------------|
| **PHP** | ^8.2 | Llenguatge de programació base del projecte |
| **laravel/framework** | ^12.0 | Framework principal. Proporciona routing, ORM (Eloquent), migracions, validació, middleware, cues, esdeveniments i tota l'arquitectura MVC |
| **laravel/sanctum** | ^4.0 | Autenticació API basada en tokens. Genera i gestiona tokens d'accés personal per protegir els endpoints de l'API REST |
| **laravel/tinker** | ^2.10.1 | REPL (consola interactiva) per a Laravel. Permet executar codi PHP directament contra l'aplicació per a debugging i proves ràpides |

---

## 🧩 Spatie (Laravel)

### 🔐 spatie/laravel-permission — Gestió de rols i permisos
Permet definir rols i permisos de manera flexible i assignar-los als usuaris. Simplifica el control d’accés a rutes i funcionalitats del sistema i s’integra amb els middleware i les polítiques de Laravel.

- Assignació de rols i permisos a usuaris.
- Validació d’accés a rutes i accions del sistema.
- Integració amb guards i middleware.
- API clara, robusta i fàcil d’estendre.

S’utilitza per gestionar l’accés segons el tipus d’usuari (Usuari, Admin, Super Admin) i garantir que només puguin executar les accions que els corresponen.

---

## 🛠️ Dependències de Desenvolupament (PHP/Composer)

| Llibreria | Versió | Descripció |
|-----------|--------|------------|
| **fakerphp/faker** | ^1.23 | Generador de dades falses (noms, emails, adreces, etc.). S'utilitza en factories i seeders per poblar la base de dades amb dades de prova |
| **laravel/pail** | ^1.2.2 | Visor de logs en temps real des de la terminal. Mostra els logs de l'aplicació amb colors i filtres |
| **laravel/pint** | ^1.24 | Formatejador de codi PHP. Aplica les regles d'estil de codi de Laravel automàticament (basat en PHP-CS-Fixer) |
| **laravel/sail** | ^1.41 | Entorn Docker lleuger per a desenvolupament amb Laravel. Proporciona ordres simplificades per gestionar contenidors |
| **mockery/mockery** | ^1.6 | Llibreria de mocking per a tests. Permet simular dependències i verificar comportaments en tests unitaris |
| **nunomaduro/collision** | ^8.6 | Millora la visualització d'errors a la consola. Mostra stack traces llegibles i amb colors quan un test o ordre falla |
| **phpunit/phpunit** | ^11.5.3 | Framework de testing unitari i d'integració. Executa els tests del projecte amb `php artisan test` |

---

## 🌐 Dependències de Frontend (NPM)

| Llibreria | Versió | Descripció |
|-----------|--------|------------|
| **vite** | ^7.0.7 | Bundler i servidor de desenvolupament ultraràpid. Compila i serveix els assets del frontend (CSS, JS) |
| **laravel-vite-plugin** | ^2.0.0 | Connector (plugin) que integra Vite amb Laravel. Gestiona la càrrega d'assets en les vistes Blade |
| **tailwindcss** | ^4.0.0 | Framework CSS utility-first. Proporciona classes CSS predefinides per a un disseny ràpid |
| **@tailwindcss/vite** | ^4.0.0 | Connector de TailwindCSS per a Vite. Processa i optimitza les classes de Tailwind durant el build |
| **axios** | ^1.11.0 | Client HTTP per a JavaScript. Realitza peticions AJAX des del frontend a l'API |
| **concurrently** | ^9.0.1 | Executa múltiples ordres en paral·lel. S'utilitza en l'script `dev` per llançar el servidor, cues, logs i Vite simultàniament |

---

## 🐳 Serveis Docker

| Servei | Imatge | Descripció |
|--------|--------|------------|
| **PostgreSQL + PostGIS** | `postgis/postgis:15-3.3` | Base de dades relacional amb extensió geoespacial per a geofencing |
| **pgAdmin 4** | `dpage/pgadmin4` | Interfície web per administrar i visualitzar la base de dades PostgreSQL |
| **Laravel API** | `php:8.4-fpm` | Contenidor amb PHP-FPM que executa l'aplicació Laravel |

---

## 🔗 Relació entre Llibreries

```text
Laravel Framework (core)
├── Sanctum → Autenticació API (tokens)
├── Tinker → Debug interactiu
├── Eloquent ORM → Models i migracions → PostgreSQL
└── Artisan CLI → Ordres (migrate, seed, test...)

Testing
├── PHPUnit → Tests unitaris/feature
├── Mockery → Mocking de dependències
├── Collision → Errors visuals a la consola
└── Faker → Dades de prova per a seeders/factories

Qualitat de Codi
└── Pint → Formateig automàtic (CI/CD)

Frontend (Assets)
├── Vite + Laravel Plugin → Compilació
├── TailwindCSS → Estils
└── Axios → Peticions HTTP
```

Última actualització: 26 de febrer de 2026
