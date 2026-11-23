# Testing & Verification — DEVOPS-6

## 1. Container Status Verification

**Command:**
```bash
docker ps
```

**Purpose:** Verify that all four application containers are running.

**Expected Containers:**
- shlink-server
- shlink-web
- shlink-db
- shlink-redis

**Result:** PASS — All four containers were running.

---

## 2. Docker Network Verification

**Command:**
```bash
docker network inspect shlink-net
```

**Purpose:** Inspect the custom Docker bridge network and verify container connectivity.

**Result:** PASS — Custom network `shlink-net` exists and is configured.

---

## 3. Shlink API Health Check

**Command:**
```bash
curl -i http://localhost:8080/rest/health
```

**Purpose:** Verify that the Shlink REST API is responding.

**Observed Response:**
```text
HTTP/1.1 200 OK
```

```json
{
  "status": "pass",
  "version": "5.1.6"
}
```

**Result:** PASS — API health check successful.

---

## 4. BusyBox Internal Connectivity Test

**Command:**
```bash
docker run --rm --network shlink-net busybox wget -qO- http://shlink-server:8080/rest/health
```

**Purpose:**
- Test Docker internal DNS resolution.
- Verify communication through the custom bridge network.
- Confirm that the Shlink server is accessible by its container name.

**Observed Response:**
```json
{
  "status": "pass",
  "version": "5.1.6"
}
```

**Result:** PASS — Internal container communication successful.

---

## 5. Web Dashboard Verification

**URL:**
```text
http://localhost:8000
```

**Purpose:** Verify that the Shlink Web Client is accessible through the browser.

**Result:** PASS — Dashboard loaded successfully and displayed URL management functionality.

---

## 6. Final Test Summary

| Test | Status |
|---|---|
| Container status | PASS |
| Custom Docker network | PASS |
| API health check | PASS |
| Internal DNS resolution | PASS |
| Internal connectivity | PASS |
| Web dashboard | PASS |

## Conclusion

All performed verification tests passed successfully.

The Shlink application is operational, all four containers are running, and communication between services through the custom Docker network has been verified.

The deployment successfully demonstrates multi-container management and Docker bridge networking using plain Docker commands.