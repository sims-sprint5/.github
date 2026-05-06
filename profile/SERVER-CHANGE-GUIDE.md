# Server Change Guide (Backend)

This guide explains what to change when moving the backend to a new server: IPs, GitHub Actions secrets, and SSH keys (who owns them, where they go).

## 1) What typically changes

- Server IP or hostname.
- SSH port (if not default 22).
- SSH user (the account used for deploy).
- Where the app lives on the server (e.g., `/var/www/sims-back`).
- Reverse proxy target (Apache/Nginx pointing to the app port).

## 2) GitHub Actions: secrets to update

These secrets are used by the deploy workflow. Update them to match the new server.

- `DEPLOY_HOST`: new server hostname or IP.
- `DEPLOY_PORT`: SSH port on the new server (e.g., 22 or 1303).
- `DEPLOY_USER`: Linux user for deploy (e.g., `ubuntu`, `deploy`, `root`).
- `DEPLOY_SSH_KEY_B64`: base64 of the private SSH key used for deploy.

Where these are used:
- Workflow: `.github/workflows/deploy-main.yml` in the deploy job.

## 3) Reverse proxy configuration

If you use Apache as a reverse proxy, update the target host/port to the new backend address (or same host with new port).

Example (Apache VirtualHost):

```
ProxyPass / http://NEW_BACKEND_IP:8080/
ProxyPassReverse / http://NEW_BACKEND_IP:8080/
```

If the backend runs on the same server, you can point to `http://127.0.0.1:8080/` instead.

## 4) SSH keys: who owns them and where they go

You need two keys in this setup:

A) Deploy key for GitHub Actions -> Server (used by CI to SSH into the server)
- Owner: GitHub Actions (the workflow runner).
- Stored in GitHub Secrets as `DEPLOY_SSH_KEY_B64` (base64 of the private key).
- Public key goes into: `~/.ssh/authorized_keys` of the deploy user on the server.
- Used in workflow step: "Setup SSH Key".

B) Repo access key on the server -> GitHub (used by server to git fetch)
- Owner: the server itself (the deploy user).
- Private key file on server: `~/.ssh/github_deploy_repo` (name used in workflow).
- Public key must be added as a Deploy Key (or to a machine user) in GitHub.
- Used in workflow step: "Pull latest code" (via `git config core.sshCommand`).

## 5) How to generate the keys

A) GitHub Actions deploy key (Actions -> Server)

On your local machine (or any secure environment):

```
ssh-keygen -t ed25519 -C "github-actions-deploy" -f deploy_key
```

- `deploy_key` is the private key.
- `deploy_key.pub` is the public key.

Install public key on the server (deploy user):

```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
cat deploy_key.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

Store private key in GitHub Secrets:

```
base64 -w 0 deploy_key
```

Copy the output into `DEPLOY_SSH_KEY_B64`.

B) Server repo access key (Server -> GitHub)

On the server (deploy user):

```
ssh-keygen -t ed25519 -C "server-github-deploy" -f ~/.ssh/github_deploy_repo
```

Add the public key (`~/.ssh/github_deploy_repo.pub`) to GitHub:
- Repository -> Settings -> Deploy Keys -> Add key
- Give read-only access (recommended) or read/write if needed.

## 6) Validate the SSH path

From GitHub Actions, the deploy job should succeed with:
- SSH login to server
- `git fetch` inside `/var/www/sims-back`
- Docker build and start

From the server, verify repo access:

```
ssh -T git@github.com
```

You should see a message confirming authentication.

## 7) Optional: update .env on the server

If the domain or IP changed, update:
- `APP_URL`
- `APP_DOMAIN`
- `FRONTEND_URL`
- `TENANT_BASE_DOMAIN`
- `SESSION_DOMAIN`
- `SANCTUM_STATEFUL_DOMAINS`
- `CORS_ALLOWED_ORIGINS`
- `CORS_ALLOWED_ORIGINS_PATTERNS`

Then restart containers:

```
docker compose up -d --build
```
