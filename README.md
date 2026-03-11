# Traefik Docker Projects

This repository contains Docker-based Traefik reverse proxy configurations for learning and production use.

## Projects

### [project_1](./project_1/) - Simple HTTPS for Docker

A basic Traefik setup demonstrating reverse proxy functionality with Docker provider integration.

**Features:**
- Traefik v3.6 reverse proxy
- Automatic service discovery via Docker labels
- HTTP (port 80) and HTTPS (port 443) entrypoints
- Built-in Traefik dashboard (port 8080)
- Whoami test service for demonstrating routing

**Quick Start:**

```bash
# Create the frontend network
docker network create frontend

# Start Traefik
cd project_1
docker compose up -d

# Start the whoami test service
docker compose -f whoami.yml up -d
```

**Access Points:**
- Traefik Dashboard: http://localhost:8080
- Whoami service: http://whoami.localhost

## Documentation

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Traefik Docker Provider](https://doc.traefik.io/traefik/providers/docker/)
- [Getting Started Guide](https://doc.traefik.io/traefik/getting-started/docker/)
