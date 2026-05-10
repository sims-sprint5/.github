# Debugging Guide — SIMS Back

## Quick Reference

```bash
cd /var/www/sims-back
export COMPOSE_FILE=docker-compose.yml

docker compose ps                          # container status
docker compose logs -f api                 # live Laravel logs
docker compose logs --tail=100 api_web     # nginx logs
docker compose exec -T api php artisan about --only=Environment
```

---

## Container Logs

```bash
# Laravel application
docker compose logs api --tail=100

# Nginx
docker compose logs api_web --tail=50

# PostgreSQL
docker compose logs postgres --tail=50

# IoT subsystem
docker compose -f ~/subsistema/docker-compose.yml logs api --tail=100

# Live log file
docker compose exec api tail -f storage/logs/laravel.log
```

---

## Artisan Diagnostics

```bash
# Clear all caches
docker compose exec -T api php artisan optimize:clear

# Rebuild caches
docker compose exec -T api php artisan optimize

# Verify DB connection
docker compose exec -T api php artisan tinker --execute="DB::connection()->getPdo(); echo 'DB OK';"

# Run pending migrations
docker compose exec -T api php artisan migrate --force

# List tenants
docker compose exec -T api php artisan tenants:list

# Reseed a specific tenant
docker compose exec -T api php artisan tenants:seed --tenants=company1
```

---

## Common Errors

### `SQLSTATE[42P01]: relation "X" does not exist`
Central migrations have not been applied.
```bash
docker compose exec -T api php artisan migrate --force
```

### `APP_KEY` not defined / all requests return 500
```bash
KEY=$(docker compose exec -T api php artisan key:generate --show)
sed -i "s|^APP_KEY=.*|APP_KEY=$KEY|" .env
docker compose up -d --no-deps --force-recreate api
```

### Login returns 401 / SuperAdmin not found
The SuperAdmin seeder did not run.
```bash
docker compose exec -T api php artisan db:seed --class=SuperAdminSeeder --force
```

### Tenant routes return 503 "Tenant database not initialized"
```bash
docker compose exec -T api php artisan tenants:migrate --force
```

### Nginx (api_web) exits immediately
iot-api hostname could not be resolved at nginx startup. Ensure `iot-api` is running before `api_web`:
```bash
docker compose -f ~/subsistema/docker-compose.yml up -d api
sleep 5
docker compose up -d --no-deps --force-recreate api_web
docker compose logs api_web --tail=20
```

### Frontend gets 404 on tenant API calls from the central domain
The tenant was not found because the request came from the central domain, not a subdomain. This is expected behaviour — the API returns `{"data":[]}` when a Bearer token is present or `{"message":"Tenant not found"}` when not.

### Frontend host detection fails (treats base domain as tenant)
Ensure the Vue build has the correct environment variables baked in:
```javascript
// Browser console
console.log(import.meta.env.VITE_APP_URL)           // should not be undefined
console.log(import.meta.env.VITE_TENANT_DOMAIN_SUFFIX)  // should not be undefined
```

---

## IoT / WebSocket Debugging

### Raspberry Pi cannot connect (HTTP 500 or 404)

**HTTP 500** — iot-api crashed at startup (likely empty `MONGO_URI`). Check logs:
```bash
docker compose -f ~/subsistema/docker-compose.yml logs api --tail=50
```
iot-api will run without MongoDB if `MONGO_URI` is unset — WebSocket and LED control still work; only temperature storage is disabled.

**HTTP 404** — The WebSocket request reached iot-api but headers were stripped by an upstream proxy (Apache/OpenResty). The proxy chain needs WebSocket support enabled:

For **Apache** (add before the generic ProxyPass rule):
```apache
LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so

ProxyPass /api/ws/ ws://localhost:8000/api/ws/
ProxyPassReverse /api/ws/ ws://localhost:8000/api/ws/
ProxyPass /ws/ ws://localhost:8000/ws/
ProxyPassReverse /ws/ ws://localhost:8000/ws/
```

For **OpenResty/nginx** (add to the server block for the domain):
```nginx
location /api/ws/ {
    proxy_pass http://127.0.0.1:8000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

### Test WebSocket handshake manually
```bash
# From the Raspberry Pi
curl -si --max-time 5 \
  -H "Upgrade: websocket" \
  -H "Connection: Upgrade" \
  -H "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==" \
  -H "Sec-WebSocket-Version: 13" \
  https://your-domain.com/api/ws/vehicle/001 | head -5
# Expected: HTTP/1.1 101 Switching Protocols
```

### LED control returns 404 (vehicle not connected)
The Pi is not connected via WebSocket. Check the Pi's client log:
```bash
tail -30 ~/Subsistema-IoT/raspberry/client_ws.log
```

### Test actuator directly
```bash
curl -X POST http://localhost:8088/api/actuator/001/on \
  -H "X-API-Key: subsistemaequip2"
# 404 = vehicle not connected; 200 = command sent to Pi
```

---

## Storage Permissions

If Laravel cannot write logs or cache:
```bash
docker compose exec -T -u root api bash -c "
  chown -R 33:33 /var/www/html/storage /var/www/html/bootstrap/cache
  chmod -R ug+rwX /var/www/html/storage /var/www/html/bootstrap/cache
"
```

---

## Full Reset (development only — destroys data)

```bash
docker compose down
docker system prune -f --all
docker network create sims_network
docker compose up -d --build
docker compose exec -T api php artisan migrate --force
docker compose exec -T api php artisan db:seed --force
docker compose exec -T api php artisan tenants:migrate --force
```
