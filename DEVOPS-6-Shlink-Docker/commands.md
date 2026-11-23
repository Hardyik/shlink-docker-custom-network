# Docker Commands — DEVOPS-6

## 1. Docker Images

```bash
docker images
```

## 2. Running Containers

```bash
docker ps
```

## 3. All Containers

```bash
docker ps -a
```

## 4. Docker Networks

```bash
docker network ls
```

## 5. Inspect Custom Network

```bash
docker network inspect shlink-net
```

## 6. Container Logs

```bash
docker logs --tail 100 shlink-server
docker logs --tail 100 shlink-db
docker logs --tail 100 shlink-redis
docker logs --tail 100 shlink-web
```

## 7. API Health Check

```bash
curl -i http://localhost:8080/rest/health
```

Expected response: HTTP 200 OK.

## 8. Internal Network Connectivity

```bash
docker run --rm --network shlink-net busybox wget -qO- http://shlink-server:8080/rest/health
```

Expected response: Shlink health JSON with status pass.

## 9. Container Inspection

```bash
docker inspect shlink-server
docker inspect shlink-db
docker inspect shlink-redis
docker inspect shlink-web
```

## 10. Container Port Mapping

```bash
docker port shlink-server
docker port shlink-web
```

## 11. Restart Containers

```bash
docker start shlink-db shlink-redis
docker start shlink-server
docker start shlink-web
```

## 12. Stop Containers

```bash
docker stop shlink-web shlink-server shlink-redis shlink-db
```

Note: These commands are for the manually deployed Docker environment. Do not publish database passwords, API keys, or other secrets in this documentation.