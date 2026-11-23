# Shlink Docker Architecture

```mermaid
flowchart TB
    U[User / Browser]

    U -->|Port 8000| W[Shlink Web Client]
    U -->|Port 8080| S[Shlink Server]

    subgraph N[Custom Docker Network: shlink-net]
        W --> S
        S -->|Port 5432| P[(PostgreSQL)]
        S -->|Port 6379| R[(Redis)]
    end
```

## Network Details

- Network name: shlink-net
- Network driver: bridge
- Shlink Server: 8080
- Web Client: 8000
- PostgreSQL: 5432
- Redis: 6379

All services communicate using Docker's internal container networking.