# Traefik Docker Reverse Proxy

A beginner-friendly Traefik v3.6 setup demonstrating reverse proxy functionality with automatic service discovery using Docker labels.

## Overview

This project sets up Traefik as a reverse proxy for Docker containers. It uses the Docker provider to automatically detect services based on container labels, eliminating the need for manual configuration.

## Prerequisites

- Docker Engine 20.10+
- Docker Compose v2+
- (Optional) Docker network named `frontend`

## Quick Start

### 1. Create the Docker Network

```bash
docker network create frontend
```

### 2. Start Traefik

```bash
docker compose up -d
```

### 3. Start the Whoami Test Service

```bash
docker compose -f whoami.yml up -d
```

### 4. Access the Services

| Service | URL |
|---------|-----|
| Traefik Dashboard | http://localhost:8080 |
| Whoami Test App | http://whoami.localhost |

## Configuration

### Traefik Configuration ([`config/traefik.yaml`](config/traefik.yaml))

The main Traefik configuration file defines:

- **Global Settings**: Disables version checks and anonymous usage reporting
- **Log Level**: DEBUG for troubleshooting
- **API**: Enables the dashboard in insecure mode
- **Entry Points**:
  - `web` (port 80): HTTP traffic
  - `websecure` (port 443): HTTPS traffic
- **Docker Provider**: Monitors Docker socket for label-based routing

### Docker Compose ([`docker-compose.yml`](docker-compose.yml))

Key configuration:

```yaml
command:
  - "--api.insecure=true"      # Enable dashboard without TLS
  - "--providers.docker=true" # Enable Docker provider
  - "--entrypoints.web.address=:80" # HTTP endpoint
```

### Service Labels

Services are discovered using Docker labels. Example from [`whoami.yml`](whoami.yml):

```yaml
labels:
  - traefik.enable=true
  - traefik.http.routers.whoami.rule=Host(`whoami.localhost`)
  - traefik.http.routers.whoami.entryPoints=web
```

**Available Label Options:**

| Label | Description | Example |
|-------|-------------|---------|
| `traefik.enable` | Enable/disable service | `true` |
| `traefik.http.routers.<name>.rule` | Routing rule | `Host(\`example.com\`)` |
| `traefik.http.routers.<name>.entryPoints` | Entry point | `web` or `websecure` |
| `traefik.http.routers.<name>.tls` | Enable TLS | `true` |
| `traefik.http.services.<name>.loadbalancer.server.port` | Backend port | `80` |

## Common Routing Rules

```yaml
# Route by domain
traefik.http.routers.mysite.rule=Host(`example.com`)

# Route by path
traefik.http.routers.mysite.rule=PathPrefix(`/api`)

# Route by domain and path
traefik.http.routers.mysite.rule=Host(`example.com`) && PathPrefix(`/api`)
```

## Managing Services

### View Running Containers

```bash
docker compose ps
```

### View Traefik Logs

```bash
docker compose logs -f traefik
```

### Stop All Services

```bash
docker compose down
docker compose -f whoami.yml down
```

### Access Traefik Container

```bash
docker exec -it traefik-project-1 sh
```

## Troubleshooting

### Services Not Being Discovered

1. Ensure the container is on the `frontend` network
2. Check that `traefik.enable=true` label is set
3. Verify Traefik has access to Docker socket:
   ```yaml
   volumes:
     - /var/run/docker.sock:/var/run/docker.sock:ro
   ```

### Cannot Access Services

1. Verify the `Host` header matches your routing rule
2. Add an entry to `/etc/hosts`:
   ```
   127.0.0.1 whoami.localhost
   ```
3. Check Traefik logs for routing decisions:
   ```bash
   docker compose logs traefik | grep whoami
   ```

### Dashboard Not Loading

Ensure the API is enabled in the command:
```yaml
command:
  - "--api.insecure=true"
```

Then access at http://localhost:8080/dashboard/

## Security Notes

- The API dashboard is running in **insecure mode** (no authentication)
- This configuration is for development/learning only
- For production, enable TLS and add authentication

## Resources

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Docker Provider](https://doc.traefik.io/traefik/providers/docker/)
- [Getting Started Tutorial](https://doc.traefik.io/traefik/getting-started/docker/)
- [Routing Configuration](https://doc.traefik.io/traefik/routing/overview/)
