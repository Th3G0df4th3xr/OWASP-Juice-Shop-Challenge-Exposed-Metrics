📄 Method-11-SSRF-to-Internal-Metrics-Endpoints/README.md
markdown
Copy
Edit
# 🛰️ Method 11 - SSRF to Internal Metrics Endpoints

## 🎯 Goal
Exploit Server-Side Request Forgery (SSRF) to access internal Prometheus or Grafana metrics endpoints that are not directly reachable externally.

---

## 🧠 Concept
SSRF vulnerabilities allow attackers to make arbitrary HTTP requests from the vulnerable server. By abusing this behavior, we can pivot into internal-only services (e.g., `localhost:9090` for Prometheus or `127.0.0.1:3000` for Grafana) to exfiltrate sensitive metrics and system telemetry.

---

## 📍 Target: OWASP Juice Shop
- Identify SSRF injection points (e.g., image URL uploaders, PDF generators, or request forwarders).
- Attempt to redirect internal requests to:
  - `http://localhost:9090/metrics`
  - `http://127.0.0.1:3000/api/dashboards`

---

## 🚀 Exploitation Steps
1. Locate a feature where the server fetches a URL (e.g., image fetcher).
2. Input internal service URLs (e.g., `http://localhost:9090/metrics`).
3. Observe server behavior or capture leaked metrics in the response.

---

## 🔍 Indicators of Success
- Server returns Prometheus-formatted metrics.
- Server returns Grafana JSON dashboard data or login pages.
- Internal services expose tokens, service names, or stack traces.

---

## 💥 Impact
- Unauthorized access to monitoring interfaces.
- Enumeration of internal IPs, services, and performance data.
- Potential to pivot deeper into internal network or discover other flaws.

---

## 🛠️ Tools Used
- Burp Suite (Repeater, Collaborator)
- `curl` or `httpie`
- Juice Shop with SSRF simulation enabled

---

## 🧪 Sample Payload
```bash
GET /?url=http://localhost:9090/metrics HTTP/1.1
Host: juice-shop.internal
⚠️ Mitigation (for devs)
Whitelist-only outgoing requests

Block internal IP ranges in SSRF logic

Use SSRF protection middleware or proxies

📎 References
OWASP SSRF Guide: https://owasp.org/www-community/attacks/Server_Side_Request_Forgery

Prometheus Metrics Format: https://prometheus.io/docs/instrumenting/exposition_formats/
