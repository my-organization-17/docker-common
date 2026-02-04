# Docker Infrastructure

This folder contains Docker Compose configuration for shared infrastructure services used by the CoffeeDoor microservices platform.

## Services

### Database Management

| Service | Port | Description |
|---------|------|-------------|
| **Adminer** | 8080 | Web-based database management UI |

### Message Broker

| Service | Port | Description |
|---------|------|-------------|
| **RabbitMQ** | 5672, 15672 | Message broker (AMQP: 5672, Management UI: 15672) |

### Observability Stack

| Service | Port | Description |
|---------|------|-------------|
| **Jaeger** | 16686, 4317, 4318 | Distributed tracing (UI: 16686, OTLP gRPC: 4317, OTLP HTTP: 4318) |
| **Prometheus** | 9090 | Metrics collection and alerting |
| **Grafana** | 3003 | Visualization and dashboards |
| **Loki** | 3100 | Log aggregation system |
| **Promtail** | - | Log collector agent (scrapes Docker logs) |

### Metrics Exporters

| Service | Port | Description |
|---------|------|-------------|
| **postgres-exporter** | 9187 | PostgreSQL metrics exporter |
| **rabbitmq-exporter** | 9419 | RabbitMQ metrics exporter |
| **redis-exporter** | 9121 | Redis metrics exporter |
| **node-exporter** | 9100 | Host system metrics exporter |

## Configuration Files

| File | Description |
|------|-------------|
| `docker-compose.yml` | Main Docker Compose configuration |
| `prometheus.yml` | Prometheus scrape configuration |
| `loki.yml` | Loki log storage configuration |
| `promtail.yml` | Promtail log collection configuration |
| `.env.example` | Environment variables template |

## Prerequisites

Create an external Docker network before starting:

```bash
docker network create coffeedoor-network
```

## Setup

1. Copy the environment template and configure variables:

```bash
cp .env.example .env
```

2. Edit `.env` with your credentials:

```env
# RabbitMQ
RABBITMQ_DEFAULT_USER=your_user
RABBITMQ_DEFAULT_PASS=your_password

# PostgreSQL (for exporter)
POSTGRES_USER=your_user
POSTGRES_PASSWORD=your_password
POSTGRES_HOST=user-database
POSTGRES_PORT=5432
POSTGRES_DB=user_db

# Grafana
GF_SECURITY_ADMIN_USER=admin
GF_SECURITY_ADMIN_PASSWORD=your_password

# Redis (for exporter)
REDIS_HOST=user-redis-db
REDIS_PORT=6379
```

3. Start the services:

```bash
docker compose up -d
```

## Access URLs

| Service | URL |
|---------|-----|
| Adminer | http://localhost:8080 |
| RabbitMQ Management | http://localhost:15672 |
| Jaeger UI | http://localhost:16686 |
| Prometheus | http://localhost:9090 |
| Grafana | http://localhost:3003 |

## Prometheus Targets

Prometheus is configured to scrape metrics from:

- `prometheus:9090` - Self-monitoring
- `node-exporter:9100` - Host metrics
- `postgres-exporter:9187` - PostgreSQL metrics
- `redis-exporter:9121` - Redis metrics
- `rabbitmq-exporter:9419` - RabbitMQ metrics
- `api-gateway:4004` - API Gateway application metrics
- `user-app:9101` - User service application metrics
- `notification-app:9102` - Notification service application metrics

## Log Retention

Loki is configured with a 7-day (168h) log retention period.

## Volumes

The following persistent volumes are created:

- `grafana-storage` - Grafana dashboards and settings
- `rabbitmq-data` - RabbitMQ message data
- `loki-data` - Loki log data
