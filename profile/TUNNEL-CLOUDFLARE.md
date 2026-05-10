# Configurar el túnel Cloudflare del Subsistema IoT

El backend de producción necesita llegar a la API del IoT (FastAPI) que corre en la máquina local del equipo. El puente entre ambos es un **túnel Cloudflare** que expone el puerto `8088` de la máquina local a internet.

---

## Por qué hay que rehacerlo

El proyecto usa **Quick Tunnels** de Cloudflare (gratuitos, sin cuenta). Cada vez que el contenedor `iot-cloudflared` se reinicia, Cloudflare asigna una URL nueva aleatoria del tipo:

```
https://inspection-asking-jets-careers.trycloudflare.com
```

Cuando cambia la URL hay que actualizarla en dos sitios:
1. El `.env` de la Raspberry Pi (`API_WS_URL`)
2. El secret de GitHub `VEHICLE_SUBSYSTEM_URL` del repo `sims-back`

---

## Paso 1 — Levantar el subsistema IoT

En la máquina del equipo donde está el repo `Subsistema-IoT`:

```bash
cd ~/Subsistema-IoT
docker compose up -d
```

Esto arranca dos contenedores:
- `iot-api` — FastAPI en el puerto `8088`
- `iot-cloudflared` — túnel Cloudflare apuntando a `http://api:8000`

---

## Paso 2 — Obtener la nueva URL del túnel

```bash
cd ~/Subsistema-IoT
docker compose logs cloudflared | grep trycloudflare
```

La salida tendrá una línea parecida a:

```
|  https://inspection-asking-jets-careers.trycloudflare.com  |
```

Copia esa URL completa (con `https://`).

---

## Paso 3 — Actualizar la Raspberry Pi

La Raspberry Pi necesita saber la nueva URL para conectarse por WebSocket a la API.

### 3a. Conectarse por SSH

```bash
ssh admin@192.168.0.112
# contraseña: admin
```

> Si no tienes `sshpass`, prueba primero con el comando directo; si pide contraseña escríbela manualmente.

### 3b. Editar el `.env` del cliente WebSocket

```bash
cd ~/raspberry          # o la ruta donde esté client_ws.py
nano .env
```

Cambia la línea `API_WS_URL` con la nueva URL (formato WebSocket, no HTTP):

```
API_WS_URL=wss://NUEVA-URL.trycloudflare.com/ws/vehicle/001
```

> Sustituye `https://` por `wss://` y añade `/ws/vehicle/001` al final.

Guarda el archivo (`Ctrl+O`, `Ctrl+X`).

### 3c. Reiniciar el cliente

```bash
# Matar el proceso anterior si está corriendo
pkill -f client_ws.py || true

# Iniciar de nuevo con el .env actualizado
cd ~/raspberry
nohup ./venv/bin/python3 -u client_ws.py >> client_ws.log 2>&1 &
disown $!
```

Verifica que conecta correctamente:

```bash
tail -20 ~/raspberry/client_ws.log
```

Deberías ver mensajes de conexión WebSocket y envío de temperatura.

---

## Paso 4 — Actualizar el secret de GitHub

El backend de producción recibe la URL del túnel a través de un secret de GitHub que se inyecta al `.env` del servidor en cada deploy.

1. Ve a **GitHub → repo `sims-back` → Settings → Secrets and variables → Actions**
2. Edita el secret `VEHICLE_SUBSYSTEM_URL`
3. Pega la nueva URL con `https://` (sin `/ws/...` — esa parte la añade el backend)

```
https://NUEVA-URL.trycloudflare.com
```

4. Guarda.

---

## Paso 5 — Forzar un redeploy del backend

Para que el servidor recoja el nuevo valor del secret, haz un push vacío o redispara el workflow manualmente:

```bash
# Opción A — push vacío
git commit --allow-empty -m "ci: actualizar URL del túnel IoT"
git push origin main

# Opción B — desde la web de GitHub
# Actions → Deploy Main to Production → Re-run all jobs
```

---

## Paso 6 — Verificar el pipeline completo

### Desde la máquina local (directo al IoT):

```bash
# Encender LED
curl -X POST http://localhost:8088/api/actuator/001/on \
     -H "X-API-Key: subsistemaequip2"

# Apagar LED
curl -X POST http://localhost:8088/api/actuator/001/off \
     -H "X-API-Key: subsistemaequip2"

# Última temperatura
curl http://localhost:8088/api/laravel/temperatures/latest \
     -H "X-API-Key: subsistemaequip2"
```

### Desde internet (a través del túnel):

```bash
curl -X POST https://NUEVA-URL.trycloudflare.com/api/actuator/001/on \
     -H "X-API-Key: subsistemaequip2"
```

Si responde `{"message":"Actuator ON sent to vehicle 001","status":"ok","state":"on"}` el túnel funciona.

### Desde el frontend:

En **Mis Reservas**, pulsa el botón morado de control en cualquier reserva activa. Si aparece la temperatura y los botones encender/apagar responden, el pipeline completo está operativo.

---

## Esquema del flujo

```
Frontend (navegador)
    │  POST /api/v1/reservations/{id}/vehicle/on
    ▼
Backend Laravel (producción)
    │  POST https://NUEVA-URL.trycloudflare.com/api/actuator/001/on
    │  Header: X-API-Key: subsistemaequip2
    ▼
Cloudflare Quick Tunnel
    │  (reenvía a localhost:8088 en la máquina local)
    ▼
FastAPI IoT API (localhost:8088 / puerto 8000 en Docker)
    │  WebSocket → Raspberry Pi
    ▼
Raspberry Pi (GPIO pin 24 → LED/actuador)
```

---

## Variables de entorno relevantes

| Dónde | Variable | Valor ejemplo |
|-------|----------|---------------|
| Backend `.env` (prod) | `VEHICLE_SUBSYSTEM_URL` | `https://xxx.trycloudflare.com` |
| Backend `.env` (prod) | `VEHICLE_SUBSYSTEM_KEY` | `subsistemaequip2` |
| Raspberry Pi `.env` | `API_WS_URL` | `wss://xxx.trycloudflare.com/ws/vehicle/001` |
| Raspberry Pi `.env` | `VEHICLE_ID` | `001` |
| Raspberry Pi `.env` | `GPIO_PIN` | `24` |

---

## Alternativa permanente (opcional)

Si el equipo tiene una cuenta de Cloudflare con un dominio propio, se puede crear un **Named Tunnel** con URL fija. Esto elimina la necesidad de actualizar la URL manualmente cada vez. Consultar la [documentación oficial de Cloudflare Tunnels](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/).
