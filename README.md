# DEVOPS-6 — Run Shlink with Plain Docker on a Custom Network

## Project Overview

This project deploys a multi-container Shlink URL-shortening stack using plain Docker Engine commands and a user-defined Docker bridge network (`shlink-net`), without Docker Compose.

## Technology Stack

- **Docker Engine** (Ubuntu / WSL2)
- **Shlink Server:** `shlinkio/shlink:5.1.6`
- **Shlink Web Client:** `shlinkio/shlink-web-client:4.8.1`
- **PostgreSQL:** `postgres:16-alpine`
- **Redis:** `redis:7-alpine`
- **BusyBox:** `busybox` (for DNS and connectivity verification)

## Architecture Diagram

![Shlink Docker Custom Network Architecture](./diagram.jpg)

## Network & Port Configuration

All four containers run on the custom bridge network `shlink-net`. Only the Shlink Server and Web Client ports are published to the host; PostgreSQL and Redis remain strictly internal.

| Service | Container Name | Image (Pinned) | Published Host Port | Internal Port |
|---|---|---|---|---|
| Shlink Server | `shlink-server` | `shlinkio/shlink:5.1.6` | `8080` (`8080->8080/tcp`) | `8080` |
| Shlink Web Client | `shlink-web` | `shlinkio/shlink-web-client:4.8.1` | `8000` (`8000->8080/tcp`) | `8080` |
| PostgreSQL | `shlink-db` | `postgres:16-alpine` | None (internal only) | `5432` |
| Redis | `shlink-redis` | `redis:7-alpine` | None (internal only) | `6379` |

## Key Technical Findings

### 1. Default Bridge vs. User-Defined Network (`shlink-net`)
- **User-Defined Network (`shlink-net`):** Docker runs an embedded DNS server at `127.0.0.11` for user-defined bridge networks. Containers attached to `shlink-net` can dynamically resolve and reach each other by container name (e.g., `shlink-db` resolved to `172.18.0.2`), avoiding hardcoded IP addresses.
- **Default `bridge` Network:** Containers on the default bridge do not get automatic container-name DNS resolution; queries fall back to the host's upstream DNS resolver (`192.168.65.7`), causing `nslookup shlink-db` to fail with `NXDOMAIN`.

### 2. Database Failure & Recovery Behavior
- **When `shlink-db` stops:** The `shlink-server` container continues running, but `GET /rest/health` probes the PostgreSQL connection, fails, and returns `HTTP/1.1 503 Service Unavailable` with `{"status":"fail",...}`. This signals reverse proxies or load balancers to stop routing traffic to the unhealthy instance.
- **When `shlink-db` restarts:** Shlink automatically re-establishes database connectivity on subsequent requests without requiring a container restart, returning `/rest/health` to `HTTP/1.1 200 OK` with `{"status":"pass",...}`.

## Security & Secrets Hygiene

- Only dummy development credentials (`dev-only-password` and `intern-dev-api-key-0001`) were used during deployment and testing.
- `SHLINK_SERVER_API_KEY` was intentionally omitted from `shlink-web` so credentials are not written into static browser-downloadable assets; the server connection was configured via the UI instead.

## Deliverables in This Repository

- `README.md` — Project overview, architecture summary, and technical notes.
- `run-commands.md` — Complete end-to-end Docker setup, smoke test, failure test, and cleanup commands.
- `testing.md` — Verification logs and outputs for network isolation, DNS resolution, smoke tests (S1–S4), and database outage recovery.
- `Architecture.md` & hand-drawn diagram — Service topology and port exposure diagram.