# Run Commands — DEVOPS-6

All commands below use pinned image versions and dummy development secrets only.

## 1. Create Custom Network

```bash
docker network create shlink-net
```

## 2. Start Internal Backing Services (No Published Ports)

```bash
docker run -d \
  --name shlink-db \
  --network shlink-net \
  -e POSTGRES_DB=shlink \
  -e POSTGRES_USER=shlink \
  -e POSTGRES_PASSWORD=dev-only-password \
  postgres:16-alpine

docker run -d \
  --name shlink-redis \
  --network shlink-net \
  redis:7-alpine
```

## 3. Start Shlink Server & Web Client (Publish Ports 8080 and 8000)

```bash
docker run -d \
  --name shlink-server \
  --network shlink-net \
  -p 8080:8080 \
  -e DEFAULT_DOMAIN=localhost:8080 \
  -e IS_HTTPS_ENABLED=false \
  -e DB_DRIVER=postgres \
  -e DB_HOST=shlink-db \
  -e DB_PORT=5432 \
  -e DB_NAME=shlink \
  -e DB_USER=shlink \
  -e DB_PASSWORD=dev-only-password \
  -e REDIS_SERVERS=tcp://shlink-redis:6379 \
  -e INITIAL_API_KEY=intern-dev-api-key-0001 \
  shlinkio/shlink:5.1.6

# Monitor startup logs until RoadRunner server reports ready
docker logs -f shlink-server

docker run -d \
  --name shlink-web \
  --network shlink-net \
  -p 8000:8080 \
  shlinkio/shlink-web-client:4.8.1
```

## 4. Network, Port & DNS Self-Tests

```bash
# Confirm all 4 containers are attached to shlink-net
docker network inspect shlink-net --format '{{range .Containers}}{{.Name}} {{end}}'

# Confirm PostgreSQL and Redis ports are NOT published to the host (expect empty output)
docker port shlink-db
docker port shlink-redis

# DNS Test A: User-defined network (succeeds via 127.0.0.11)
docker run --rm --network shlink-net busybox nslookup shlink-db

# DNS Test B: Default bridge network (fails with NXDOMAIN)
docker run --rm busybox nslookup shlink-db
```

## 5. Application Smoke Test (S1–S4)

```bash
KEY=intern-dev-api-key-0001
H=http://localhost:8080

# Health check
curl -i http://localhost:8080/rest/health

# S2: Create short URL with custom slug "smoke"
curl -s -X POST $H/rest/v3/short-urls \
  -H "X-Api-Key: $KEY" \
  -H "Content-Type: application/json" \
  -d '{"longUrl":"https://example.com","customSlug":"smoke"}'

# S3: Verify 302 redirect to https://example.com
curl -s -o /dev/null -w "%{http_code} %{redirect_url}\n" $H/smoke

# S4: Verify visit count >= 1
sleep 2
curl -s -H "X-Api-Key: $KEY" $H/rest/v3/short-urls/smoke/visits
```

## 6. Database Failure & Recovery Test

```bash
# Stop PostgreSQL and verify HTTP 503 response
docker stop shlink-db
curl -i -s http://localhost:8080/rest/health

# Inspect server logs
docker logs --tail 25 shlink-server

# Restart PostgreSQL and verify recovery to HTTP 200 pass
docker start shlink-db
sleep 5
curl -i -s http://localhost:8080/rest/health
```

## 7. Final Environment Cleanup

```bash
docker rm -f shlink-web shlink-server shlink-redis shlink-db
docker network rm shlink-net

# Verify clean state
docker ps -a
docker network ls
```