# IoT LED Setup — Apache Proxy (Setup Actual)

Documenta com funciona la connexió Raspberry Pi → IoT API → LED amb el proxy Apache de l'institut.
Substitueix l'antic sistema de túnels Cloudflare per a entorns on la Raspberry i el servidor estan a la mateixa xarxa escolar.

---

## Arquitectura

```
Raspberry Pi (192.168.226.159)
    │  WebSocket wss://jilsoftwares.deltahost.asix2.iesmontsia.cat/ws/vehicle/001
    ▼
Apache (gateway institut)
    │  mod_proxy_wstunnel → ws://192.168.110.243:8088/ws/vehicle/001
    ▼
IoT API — FastAPI (Docker, port 8088 del servidor)
    │  WebSocket manager registra vehicle "001" com available: true
    │  POST /api/actuator/001/on  →  missatge WS {"command": "ON"}
    ▼
Raspberry Pi client_ws.py
    │  rep {"command": "ON"}
    ▼
GPIO pin 24 → LED encès
```

---

## Configuració Apache (gateway institut)

Fitxer: `/etc/apache2/sites-enabled/jilsoftwares.conf`

```apache
<VirtualHost *:80>
    ServerName jilsoftwares.deltahost.asix2.iesmontsia.cat
    ServerAlias *.jilsoftwares.deltahost.asix2.iesmontsia.cat
    ProxyPreserveHost On

    # WebSocket — HA D'ANAR ABANS del ProxyPass /
    RewriteEngine On
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/ws/(.*)$ ws://192.168.110.243:8088/ws/$1 [P,L]

    ProxyPass /ws ws://192.168.110.243:8088/ws
    ProxyPassReverse /ws ws://192.168.110.243:8088/ws

    ProxyPass /api http://192.168.110.243:8000/api
    ProxyPassReverse /api http://192.168.110.243:8000/api

    ProxyPass /assets http://192.168.110.243:8080/assets
    ProxyPassReverse /assets http://192.168.110.243:8080/assets

    ProxyPass / http://192.168.110.243:8080/
    ProxyPassReverse / http://192.168.110.243:8080/
</VirtualHost>
```

> **Ordre crític:** `/ws` i el RewriteRule han d'anar **abans** de `/`, perquè Apache fa match en ordre i `/` captura tot.

Mòduls necessaris:
```bash
sudo a2enmod proxy proxy_http proxy_wstunnel rewrite
sudo systemctl reload apache2
```

---

## Configuració Raspberry Pi

**Fitxer:** `~/Subsistema-IoT/raspberry/.env`

```env
IOT_API_KEY="subsistemaequip2"
API_WS_URL="wss://jilsoftwares.deltahost.asix2.iesmontsia.cat/ws/vehicle/001"
VEHICLE_ID="001"
GPIO_PIN=24
ACTIVE_LOW=0
```

**Arrancar el client WebSocket:**

```bash
cd ~/Subsistema-IoT/raspberry
set -a && . ./.env && set +a
nohup ./venv/bin/python3 -u client_ws.py > client_ws.log 2>&1 &
```

Verificar connexió:
```bash
tail -f ~/Subsistema-IoT/raspberry/client_ws.log
# Ha de mostrar: [001] Connected!
```

> Si el venv no té les dependències: `pip install -r requirements.txt` dins del venv.

---

## Verificar que el vehicle està connectat

Des del servidor de producció:
```bash
curl -H 'X-API-Key: subsistemaequip2' http://localhost:8088/api/actuator/001/status
# "available": true → Raspberry connectada
# "available": false → no hi ha connexió WebSocket activa
```

---

## Encendre / apagar el LED

Des del servidor (SSH):
```bash
# Encendre
curl -X POST -H 'X-API-Key: subsistemaequip2' http://localhost:8088/api/actuator/001/on

# Apagar
curl -X POST -H 'X-API-Key: subsistemaequip2' http://localhost:8088/api/actuator/001/off
```

Des del backend Laravel (automàtic quan l'usuari prem el botó al frontend):
```
POST /api/v1/reservations/{id}/vehicle/on
POST /api/v1/reservations/{id}/vehicle/off
```
> Només funciona durant el període actiu de la reserva i si l'usuari és el propietari.

---

## Diagnosi de problemes

| Símptoma | Causa | Solució |
|----------|-------|---------|
| `Connection refused` a la Raspberry | Port 8088 bloquejat per firewall | Usar el proxy Apache (URL del domini) |
| `HTTP 200` en lloc de `101 Switching Protocols` | `mod_proxy_wstunnel` no activat o ordre incorrecte al VirtualHost | `a2enmod proxy_wstunnel` + moure `/ws` abans de `/` |
| `available: false` a l'API | `client_ws.py` no arrancada o crashing | Comprovar log: `tail -f client_ws.log` |
| `Vehicle 001 not connected` al curl | Raspberry desconnectada del WebSocket | Rearrancar `client_ws.py` a la Raspberry |
| Script arranca però usa `ws://127.0.0.1:8088` | `.env` no carregat | Usar `set -a && . ./.env && set +a` abans de python |

---

## Ports del servidor (192.168.110.243 / 185.13.77.175)

| Port | Servei |
|------|--------|
| 8000 | sims-back (nginx → PHP-FPM Laravel) |
| 8080 | Frontend (sprint4-blink-prod) |
| 8088 | IoT API (FastAPI) |
| 1205 | SSH |
