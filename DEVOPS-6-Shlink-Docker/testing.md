# Testing & Verification Evidence — DEVOPS-6

## 1. Network Attachment & Port Isolation

**Commands:**
```bash
docker network inspect shlink-net --format '{{range .Containers}}{{.Name}} {{end}}'
docker port shlink-db
docker port shlink-redis
```

**Observed Output:**
```text
shlink-server shlink-web shlink-redis shlink-db
```
*(Both `docker port shlink-db` and `docker port shlink-redis` returned empty output, confirming only ports `8080` and `8000` are published.)*

---

## 2. DNS Resolution Comparison (User-Defined Network vs. Default Bridge)

**Commands:**
```bash
docker run --rm --network shlink-net busybox nslookup shlink-db
docker run --rm busybox nslookup shlink-db
```

**Observed Output:**
```text
# On shlink-net:
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:
Name:   shlink-db
Address: 172.18.0.2

# On default bridge:
Server:         192.168.65.7
Address:        192.168.65.7:53

Non-authoritative answer:
** server can't find shlink-db: NXDOMAIN
```

---

## 3. Application Smoke Test (S1–S4)

- **S1 (Web UI):** Accessed `http://localhost:8000` (`HTTP/1.1 200 OK`) and connected server `local` at `http://localhost:8080`.
- **S2 (Create Short URL):**
  ```json
  {"shortUrl":"http://localhost:8080/smoke","shortCode":"smoke","longUrl":"https://example.com","title":"Example Domain"}
  ```
- **S3 (Redirect Check):**
  ```text
  302 https://example.com/
  ```
- **S4 (Visit Tracking):**
  ```json
  {"visits":{"data":[{"date":"2026-11-25T13:51:06+00:00","userAgent":"curl/8.18.0","potentialBot":true,"visitedUrl":"http://localhost:8080/smoke","redirectUrl":"https://example.com"}],"pagination":{"currentPage":1,"pagesCount":1,"itemsPerPage":1,"itemsInCurrentPage":1,"totalItems":1}}}
  ```

---

## 4. Database Outage & Recovery Test

**Commands:**
```bash
docker stop shlink-db
curl -i -s http://localhost:8080/rest/health
docker start shlink-db && sleep 5
curl -i -s http://localhost:8080/rest/health
```

**Observed Output (While `shlink-db` Stopped):**
```http
HTTP/1.1 503 Service Unavailable
Content-Type: application/health+json

{"status":"fail","version":"5.1.6","links":{"about":"https://shlink.io","project":"https://github.com/shlinkio/shlink"}}
```

**Observed Output (After `shlink-db` Restarted):**
```http
HTTP/1.1 200 OK
Content-Type: application/health+json

{"status":"pass","version":"5.1.6","links":{"about":"https://shlink.io","project":"https://github.com/shlinkio/shlink"}}
```

---

## 5. Final Cleanup Verification

**Commands:**
```bash
docker rm -f shlink-web shlink-server shlink-redis shlink-db
docker network rm shlink-net
docker ps -a
docker network ls
```

**Result:** PASS — All task containers and `shlink-net` were removed cleanly.