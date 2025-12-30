---
layout: default
title: Deployment
---

# Deployment

This guide covers deploying the Fluvie MCP Server in various environments.

## Quick Start

### Local Development

```bash
# Clone and install
git clone https://github.com/SimonErich/fluvie_mcp_server.git
cd fluvie_mcp_server
dart pub get

# Run
dart run bin/server.dart
```

Server starts at `http://localhost:8080`

### Docker

```bash
# Build and run
docker build -t fluvie-mcp .
docker run -p 8080:8080 fluvie-mcp
```

### Docker Compose

```bash
docker-compose up -d
```

---

## Configuration

### Environment Variables

Create a `.env` file or set environment variables:

```bash
# Server Configuration
PORT=8080                    # HTTP port
HOST=0.0.0.0                 # Bind address

# Documentation
DOCS_PATH=../doc             # Path to Fluvie docs

# Logging
LOG_LEVEL=info               # debug, info, warning, error

# Security (optional)
API_KEY=                     # Optional API key requirement
CORS_ORIGINS=*               # Allowed CORS origins
RATE_LIMIT=100               # Requests per minute per IP

# HTTPS (for Traefik)
ACME_EMAIL=admin@example.com # Let's Encrypt email
```

### Documentation Mount

The server needs access to Fluvie documentation files. Mount them at runtime:

```bash
# Docker run
docker run -p 8080:8080 \
  -v /path/to/fluvie/doc:/app/data/docs:ro \
  fluvie-mcp

# Docker Compose (already configured)
volumes:
  - ../fluvie/doc:/app/data/docs:ro
```

---

## Production Deployment

### Docker Production Build

The Dockerfile uses multi-stage builds for optimal production images:

```dockerfile
# Stage 1: Build
FROM dart:stable AS build
WORKDIR /app
COPY . .
RUN dart pub get
RUN dart compile exe bin/server.dart -o bin/server

# Stage 2: Runtime
FROM debian:bookworm-slim
COPY --from=build /app/bin/server /app/server
CMD ["/app/server"]
```

Build and run:

```bash
docker build -t fluvie-mcp:latest .
docker run -d \
  --name fluvie-mcp \
  --restart unless-stopped \
  -p 8080:8080 \
  -v /path/to/docs:/app/data/docs:ro \
  fluvie-mcp:latest
```

### With Traefik (HTTPS)

For production with automatic HTTPS via Let's Encrypt:

```bash
# Set your email for certificates
export ACME_EMAIL=admin@yourdomain.com

# Start with Traefik profile
docker-compose --profile with-proxy up -d
```

This configures:
- Automatic SSL certificates via Let's Encrypt
- HTTP to HTTPS redirect
- Reverse proxy to the MCP server

### Docker Compose Production

```yaml
version: '3.8'

services:
  mcp:
    image: fluvie-mcp:latest
    restart: unless-stopped
    environment:
      - PORT=8080
      - LOG_LEVEL=info
    volumes:
      - ./docs:/app/data/docs:ro
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  traefik:
    image: traefik:v2.10
    command:
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.letsencrypt.acme.httpchallenge=true"
      - "--certificatesresolvers.letsencrypt.acme.email=${ACME_EMAIL}"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./letsencrypt:/letsencrypt
```

---

## Cloud Deployment

### Google Cloud Run

```bash
# Build and push
gcloud builds submit --tag gcr.io/PROJECT_ID/fluvie-mcp

# Deploy
gcloud run deploy fluvie-mcp \
  --image gcr.io/PROJECT_ID/fluvie-mcp \
  --platform managed \
  --allow-unauthenticated \
  --port 8080
```

### AWS ECS

Create a task definition:

```json
{
  "family": "fluvie-mcp",
  "containerDefinitions": [{
    "name": "fluvie-mcp",
    "image": "your-registry/fluvie-mcp:latest",
    "portMappings": [{
      "containerPort": 8080
    }],
    "healthCheck": {
      "command": ["CMD-SHELL", "curl -f http://localhost:8080/health || exit 1"]
    }
  }]
}
```

### Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fluvie-mcp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: fluvie-mcp
  template:
    metadata:
      labels:
        app: fluvie-mcp
    spec:
      containers:
      - name: fluvie-mcp
        image: fluvie-mcp:latest
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: fluvie-mcp
spec:
  selector:
    app: fluvie-mcp
  ports:
  - port: 80
    targetPort: 8080
```

---

## Monitoring

### Health Checks

The `/health` endpoint returns:

```json
{
  "status": "healthy",
  "version": "1.0.0",
  "uptime": 3600,
  "indices": {
    "docs": 75,
    "templates": 24
  }
}
```

### Logging

Configure log level via `LOG_LEVEL`:

- `debug` - Verbose debugging
- `info` - General operation (default)
- `warning` - Warnings only
- `error` - Errors only

Logs are written to stdout in JSON format for easy parsing.

### Metrics

For Prometheus metrics, the server exposes `/metrics` (when enabled):

```
fluvie_mcp_requests_total{method="tools/call",tool="searchDocs"} 1234
fluvie_mcp_request_duration_seconds{method="tools/call"} 0.045
```

---

## Security Considerations

1. **Read-only access** - The server only reads documentation files
2. **No authentication by default** - Add API key if needed
3. **Rate limiting** - Protect against abuse
4. **CORS** - Configure allowed origins for web clients
5. **HTTPS** - Always use HTTPS in production
