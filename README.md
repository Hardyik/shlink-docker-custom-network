# DEVOPS-6: Run Shlink with Plain Docker on a Custom Network

## Project Overview

This project demonstrates deploying Shlink, a self-hosted URL shortener, using plain Docker containers connected through a custom Docker bridge network.

## Technologies Used

- Docker Desktop
- Ubuntu (WSL2)
- PostgreSQL 16
- Redis 7
- Shlink Server
- Docker Networking

## Architecture

The application consists of four containers:

1. Shlink Server – Handles URL creation and redirection.
2. Shlink Web Client – Provides the web interface.
3. PostgreSQL – Stores application data.
4. Redis – Provides caching and related services.

All containers communicate through a custom Docker network named `shlink-net`.

## Current Implementation

- Created the custom Docker network `shlink-net`.
- Started PostgreSQL and Redis containers.
- Deployed Shlink Server.
- Configured database connectivity.
- Verified container networking.
- Successfully initialized the database.
- Verified Shlink health endpoint.
- Created and listed a test short URL.

## Environment

| Component | Version |
|---|---|
| Docker | 29.9.3 |
| Docker Compose | 5.7.0 |
| PostgreSQL | 16 Alpine |
| Redis | 7 Alpine |
| Shlink Server | 5.1.8 (currently deployed) |

**Version note:** The final task acceptance requires checking the mentor-specified pinned image versions. The current deployment uses the `stable` tag and needs to be aligned with the required pinned version.

## Health Check

Endpoint:

`http://localhost:8080/rest/v3/health`

Observed result:

`"status": "pass"`

## Docker Network

Network name: `shlink-net`

Driver: `bridge`

## Pending Work

- Deploy Shlink Web Client.
- Complete browser smoke tests S1–S4.
- Verify container DNS resolution.
- Test database failure and recovery.
- Capture final evidence.
- Clean up containers and network.

## Security

Only dummy development credentials are used. Actual passwords, API keys and GeoLite2 license keys must never be committed.

## Learning Outcomes

- Docker container management.
- Custom bridge networking.
- Container-to-container communication.
- PostgreSQL and Redis integration.
- Application health checks.
- Troubleshooting multi-container deployments.

## Project Status

In Progress – Initial Docker environment, database, Redis and Shlink Server deployment completed.
