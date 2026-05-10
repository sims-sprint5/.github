# Configure the Cloudflare Tunnel for the IoT Subsystem

The production backend needs to reach the IoT API (FastAPI) running on the team's local machine. The bridge between both is a **Cloudflare tunnel** that exposes port `8088` of the local machine to the internet.

---

## Why it needs to be redone

The project uses Cloudflare **Quick Tunnels** (free, no account required). Every time the `iot-cloudflared` container restarts, Cloudflare assigns a new random URL of the form:

```
https://inspection-asking-jets-careers.trycloudflare.com
```

When the URL changes it must be updated in two places:
1. The Raspberry Pi's `.env` file (`API_WS_URL`)
2. The GitHub secret `VEHICLE_SUBSYSTEM_URL` in the `sims-back` repo

---

## Step 1 — Start the IoT subsystem

On the team's machine where the `Subsistema-IoT` repo is located:

```bash
cd ~/Subsistema-IoT
docker compose up -d
```

This starts two containers:
- `iot-api` — FastAPI on port `8088`
- `iot-cloudflared` — Cloudflare tunnel pointing to `http://api:8000`

---

## Step 2 — Get the new tunnel URL

```bash
cd ~/Subsistema-IoT
docker compose logs cloudflared | grep trycloudflare
```

The output will have a line similar to:

```
|  https://inspection-asking-jets-careers.trycloudflare.com  |
```

Copy that full URL (including `https://`).

---

## Step 3 — Update the Raspberry Pi

The Raspberry Pi needs to know the new URL to connect via WebSocket to the API.

### 3a. Connect via SSH

```bash
ssh admin@192.168.0.112
# password: admin
```

> If you don't have `sshpass`, try the direct command first; if it asks for a password, type it manually.

### 3b. Edit the WebSocket client `.env` file

```bash
cd ~/raspberry          # or the path where client_ws.py is located
nano .env
```

Change the `API_WS_URL` line with the new URL (WebSocket format, not HTTP):

```
API_WS_URL=wss://NEW-URL.trycloudflare.com/ws/vehicle/001
```

> Replace `https://` with `wss://` and add `/ws/vehicle/001` at the end.

Save the file (`Ctrl+O`, `Ctrl+X`).

### 3c. Restart the client

```bash
# Kill the previous process if it is running
pkill -f client_ws.py || true

# Start again with the updated .env
cd ~/raspberry
nohup ./venv/bin/python3 -u client_ws.py >> client_ws.log 2>&1 &
disown $!
```

Verify it connects correctly:

```bash
tail -20 ~/raspberry/client_ws.log
```

You should see WebSocket connection messages and temperature readings being sent.

---

## Step 4 — Update the GitHub secret

The production backend receives the tunnel URL through a GitHub secret that is injected into the server's `.env` on every deploy.

1. Go to **GitHub → `sims-back` repo → Settings → Secrets and variables → Actions**
2. Edit the secret `VEHICLE_SUBSYSTEM_URL`
3. Paste the new URL with `https://` (without `/ws/...` — that part is added by the backend)

```
https://NEW-URL.trycloudflare.com
```

4. Save.

---

## Step 5 — Force a backend redeploy

For the server to pick up the new secret value, make an empty push or manually re-trigger the workflow:

```bash
# Option A — empty push
git commit --allow-empty -m "ci: update IoT tunnel URL"
git push origin main

# Option B — from the GitHub web interface
# Actions → Deploy Main to Production → Re-run all jobs
```

---

## Step 6 — Verify the complete pipeline

### From the local machine (direct to IoT):

```bash
# Turn LED on
curl -X POST http://localhost:8088/api/actuator/001/on \
     -H "X-API-Key: subsistemaequip2"

# Turn LED off
curl -X POST http://localhost:8088/api/actuator/001/off \
     -H "X-API-Key: subsistemaequip2"

# Latest temperature
curl http://localhost:8088/api/laravel/temperatures/latest \
     -H "X-API-Key: subsistemaequip2"
```

### From the internet (through the tunnel):

```bash
curl -X POST https://NEW-URL.trycloudflare.com/api/actuator/001/on \
     -H "X-API-Key: subsistemaequip2"
```

If it responds with `{"message":"Actuator ON sent to vehicle 001","status":"ok","state":"on"}` the tunnel is working.

### From the frontend:

In **My Reservations**, click the purple control button on any active reservation. If the temperature appears and the on/off buttons respond, the complete pipeline is operational.

---

## Flow diagram

```
Frontend (browser)
    │  POST /api/v1/reservations/{id}/vehicle/on
    ▼
Laravel Backend (production)
    │  POST https://NEW-URL.trycloudflare.com/api/actuator/001/on
    │  Header: X-API-Key: subsistemaequip2
    ▼
Cloudflare Quick Tunnel
    │  (forwards to localhost:8088 on the local machine)
    ▼
FastAPI IoT API (localhost:8088 / port 8000 in Docker)
    │  WebSocket → Raspberry Pi
    ▼
Raspberry Pi (GPIO pin 24 → LED/actuator)
```

---

## Relevant environment variables

| Where | Variable | Example value |
|-------|----------|---------------|
| Backend `.env` (prod) | `VEHICLE_SUBSYSTEM_URL` | `https://xxx.trycloudflare.com` |
| Backend `.env` (prod) | `VEHICLE_SUBSYSTEM_KEY` | `subsistemaequip2` |
| Raspberry Pi `.env` | `API_WS_URL` | `wss://xxx.trycloudflare.com/ws/vehicle/001` |
| Raspberry Pi `.env` | `VEHICLE_ID` | `001` |
| Raspberry Pi `.env` | `GPIO_PIN` | `24` |

---

## Permanent alternative (optional)

If the team has a Cloudflare account with their own domain, a **Named Tunnel** with a fixed URL can be created. This eliminates the need to manually update the URL every time. See the [official Cloudflare Tunnels documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/).
