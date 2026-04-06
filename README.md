# Server Setup Script

A small, practical script set to bootstrap a fresh Ubuntu/Debian server with:

- Docker Engine + Docker Compose plugin
- Portainer
- Nginx Proxy Manager (NPM)
- Optional Cloudflared tunnel
- UFW firewall rules

It is designed for quick self-hosting setup without manually wiring every container.

Supported OS: Ubuntu and Debian servers only.

## What this repo includes

- `setup.sh`: Installs and configures the stack
- `uninstall.sh`: Removes the stack (and optionally Docker + data)
- `.env.example`: Example configuration values

## Who this is for

Use this if you want to go from a fresh VPS to a managed Docker + reverse proxy setup fast.

If you prefer fully manual Docker Compose files and custom hardening from day one, this can still be a good starting point to adapt.

## Requirements

- Ubuntu or Debian-based server
- Root privileges (`sudo`)
- Internet access for package and image downloads

## Quick start

1. Clone this repo on your server.
2. Copy the env template:

```bash
cp .env.example .env
```

3. (Optional) Edit `.env` values.
4. Make scripts executable:

```bash
chmod +x setup.sh uninstall.sh
```

5. Run setup:

```bash
sudo ./setup.sh
```

The script will:

- update system packages
- install Docker
- create a Docker network
- deploy Portainer
- deploy Nginx Proxy Manager
- optionally configure Cloudflared
- configure UFW rules

## Configuration (`.env`)

Copy `.env.example` to `.env`, then adjust as needed.

Common values:

- `NPM_HTTP_PORT` (default: `80`)
- `NPM_HTTPS_PORT` (default: `443`)
- `NPM_ADMIN_PORT` (default: `81`)
- `PORTAINER_PORT` (example file uses `9443`)
- `CLOUDFLARED_TOKEN` (leave empty to be prompted)
- `DOCKER_NETWORK` (example file uses `proxy`)
- `DATA_DIR` (example file uses `/opt/server-data`)

## After setup: first login checklist

1. Open Portainer and set a strong admin password.
2. Open Nginx Proxy Manager and change default credentials immediately.
3. Add proxy hosts and SSL certs in NPM.
4. If using Cloudflare Tunnel, validate your tunnel route mappings.

## Uninstall

Run:

```bash
sudo ./uninstall.sh
```

The uninstall script will:

- stop/remove the project containers
- remove the Docker network used by the stack
- ask whether to delete persistent data
- ask whether to uninstall Docker completely

## Notes before production

This project is very useful for quick bootstrapping, but review scripts before using in production.

A few things to keep in mind:

- Port values and network variable names should stay consistent between `.env`, setup, and uninstall flows.
- NPM default credentials are temporary only; change them on first login.
- Always confirm UFW rules match your actual SSH port before enabling firewall rules.

## Troubleshooting

### Script says command not found or syntax error

Make sure you are using bash:

```bash
bash --version
sudo bash ./setup.sh
```

### Containers are running but UI is not reachable

- Check firewall rules: `sudo ufw status`
- Check container state: `docker ps`
- Check logs:

```bash
docker logs portainer
```

```bash
docker logs npm
```

### Cloudflared did not start

- Confirm `CLOUDFLARED_TOKEN` is valid
- Check logs:

```bash
docker logs cloudflared
```

## License

See `LICENSE` for license terms.
