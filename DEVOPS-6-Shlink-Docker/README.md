# DEVOPS-6 — Run Shlink with Plain Docker on a Custom Network

## Project Overview

This project demonstrates the deployment of a multi-container Shlink URL-shortening application using Docker Engine and a custom Docker bridge network.

The deployment uses plain Docker commands without Docker Compose.

## Technology Stack

- Docker Engine
- Shlink Server 5.1.6
- Shlink Web Client 4.8.1
- PostgreSQL 16
- Redis 7
- BusyBox (network testing)
- Linux / Ubuntu environment

## Architecture

The application consists of four containers:

1. **Shlink Server** — Handles URL shortening and REST API requests.
2. **Shlink Web Client** — Provides the web dashboard.
3. **PostgreSQL** — Stores application data.
4. **Redis** — Provides caching and supporting services.

All four containers communicate through a custom Docker bridge network named `shlink-net`.

## Network Configuration

| Service | Container | Port |
|---|---|---|
| Shlink Server | shlink-server | 8080 |
| Web Client | shlink-web | 8000 |
| PostgreSQL | shlink-db | 5432 |
| Redis | shlink-redis | 6379 |

The server and web client ports are published to the host. PostgreSQL and Redis are accessed internally through Docker networking.

## Implementation

- Created a custom Docker bridge network.
- Deployed PostgreSQL and Redis containers.
- Configured and deployed the Shlink server.
- Deployed the Shlink web client.
- Configured inter-container communication.
- Verified application health using the REST API.
- Tested internal container connectivity using BusyBox.

## Verification Results

| Test | Result |
|---|---|
| All four containers running | PASS |
| Custom Docker network | PASS |
| Shlink API health check | PASS |
| Internal DNS resolution | PASS |
| Container-to-container connectivity | PASS |
| Web dashboard accessibility | PASS |

## Access

- Web Dashboard: http://localhost:8000
- Shlink API: http://localhost:8080

## Conclusion

Successfully deployed the Shlink URL-shortening application using plain Docker commands and a custom Docker bridge network.

Verified that all four containers were running, internal service communication was working, the API health endpoint returned HTTP 200, and the web dashboard was accessible.

This project provided practical experience with Docker container management, custom networking, service communication, and application deployment.