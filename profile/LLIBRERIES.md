# Librerías del Backend

Listado de todas las librerías utilizadas en el proyecto, su versión y propósito.

---

## 📦 Dependencias de Producción (PHP/Composer)

| Librería | Versión | Descripción |
|----------|---------|-------------|
| **PHP** | ^8.2 | Lenguaje de programación base del proyecto |
| **laravel/framework** | ^12.0 | Framework principal. Proporciona routing, ORM (Eloquent), migraciones, validación, middleware, colas, eventos y toda la arquitectura MVC |
| **laravel/sanctum** | ^4.0 | Autenticación API basada en tokens. Genera y gestiona tokens de acceso personal para proteger los endpoints de la API REST |
| **laravel/tinker** | ^2.10.1 | REPL (consola interactiva) para Laravel. Permite ejecutar código PHP directamente contra la aplicación para debugging y pruebas rápidas |

---

## 🛠️ Dependencias de Desarrollo (PHP/Composer)

| Librería | Versión | Descripción |
|----------|---------|-------------|
| **fakerphp/faker** | ^1.23 | Generador de datos falsos (nombres, emails, direcciones, etc.). Se usa en factories y seeders para poblar la base de datos con datos de prueba |
| **laravel/pail** | ^1.2.2 | Visor de logs en tiempo real desde la terminal. Muestra los logs de la aplicación con colores y filtros |
| **laravel/pint** | ^1.24 | Formateador de código PHP. Aplica las reglas de estilo de código de Laravel automáticamente (basado en PHP-CS-Fixer) |
| **laravel/sail** | ^1.41 | Entorno Docker ligero para desarrollo con Laravel. Proporciona comandos simplificados para gestionar contenedores |
| **mockery/mockery** | ^1.6 | Librería de mocking para tests. Permite simular dependencias y verificar comportamientos en tests unitarios |
| **nunomaduro/collision** | ^8.6 | Mejora la visualización de errores en la consola. Muestra stack traces legibles y con colores cuando un test o comando falla |
| **phpunit/phpunit** | ^11.5.3 | Framework de testing unitario y de integración. Ejecuta los tests del proyecto con `php artisan test` |

---

## 🌐 Dependencias de Frontend (NPM)

| Librería | Versión | Descripción |
|----------|---------|-------------|
| **vite** | ^7.0.7 | Bundler y servidor de desarrollo ultrarrápido. Compila y sirve los assets del frontend (CSS, JS) |
| **laravel-vite-plugin** | ^2.0.0 | Plugin que integra Vite con Laravel. Gestiona la carga de assets en las vistas Blade |
| **tailwindcss** | ^4.0.0 | Framework CSS utility-first. Proporciona clases CSS predefinidas para diseño rápido |
| **@tailwindcss/vite** | ^4.0.0 | Plugin de TailwindCSS para Vite. Procesa y optimiza las clases de Tailwind durante el build |
| **axios** | ^1.11.0 | Cliente HTTP para JavaScript. Realiza peticiones AJAX desde el frontend a la API |
| **concurrently** | ^9.0.1 | Ejecuta múltiples comandos en paralelo. Se usa en el script `dev` para lanzar el servidor, colas, logs y Vite simultáneamente |

---

## 🐳 Servicios Docker

| Servicio | Imagen | Descripción |
|----------|--------|-------------|
| **PostgreSQL + PostGIS** | `postgis/postgis:15-3.3` | Base de datos relacional con extensión geoespacial para geofencing |
| **pgAdmin 4** | `dpage/pgadmin4` | Interfaz web para administrar y visualizar la base de datos PostgreSQL |
| **Laravel API** | `php:8.4-fpm` | Contenedor con PHP-FPM que ejecuta la aplicación Laravel |

---

## 🔗 Relación entre Librerías

```
Laravel Framework (core)
├── Sanctum → Autenticación API (tokens)
├── Tinker → Debug interactivo
├── Eloquent ORM → Modelos y migraciones → PostgreSQL
└── Artisan CLI → Comandos (migrate, seed, test...)

Testing
├── PHPUnit → Tests unitarios/feature
├── Mockery → Mocking de dependencias
├── Collision → Errores bonitos en consola
└── Faker → Datos de prueba para seeders/factories

Calidad de Código
└── Pint → Formateo automático (CI/CD)

Frontend (Assets)
├── Vite + Laravel Plugin → Compilación
├── TailwindCSS → Estilos
└── Axios → Peticiones HTTP
```

---

**Última actualización:** 24 de febrero de 2026
