📁 Method-10-Check-for-Open-Grafana-or-Prometheus-Dashboards/TODO.md
markdown
Copy
Edit
# ✅ TODO - Method-10: Check for Open Grafana or Prometheus Dashboards

## 🎯 Objective
Identify exposed Grafana or Prometheus dashboards linked with OWASP Juice Shop to enumerate sensitive metrics, internal network data, and service information.

---

## 🧪 Step-by-Step Tasks

### [ ] 1. Identify Target Host
- Use `nmap`, `netstat`, or traffic logs to locate the application IP and any open ports (common: 3000 for Grafana, 9090 for Prometheus).
  ```bash
  nmap -sV <juice_shop_ip> -p 3000,9090
🔍 Checks if Grafana or Prometheus is running.

💡 Grafana usually runs on port 3000, Prometheus on 9090.

[ ] 2. Probe for HTTP Services
Use curl or a browser to access:

bash
Copy
Edit
curl http://<target_ip>:3000
curl http://<target_ip>:9090
Expect JSON, login panel, or auto-exposed dashboards.

🚨 If dashboards are accessible without auth, proceed.

[ ] 3. Bypass Login (If Present)
Check for default credentials:

Grafana: admin:admin, admin:password

Prometheus usually doesn’t have auth by default.

Try weak credential brute-force if allowed.

[ ] 4. Analyze Dashboard Content
In Grafana:

Navigate to Dashboards → Browse → Juice Shop Metrics.

Look for visual panels exposing:

System IPs

CPU/Memory stats

App-specific logs

In Prometheus:

Query endpoints like:

bash
Copy
Edit
http://<target_ip>:9090/targets
http://<target_ip>:9090/metrics
These give live service status, memory, API calls, uptime.

[ ] 5. Scrape and Enumerate Metrics
Use:

bash
Copy
Edit
wget http://<target_ip>:9090/metrics -O metrics.txt
Inspect for tokens, service calls, internal routes.

[ ] 6. Document Sensitive Findings
Look for:

JWTs

API keys

Internal usernames

Uptime of internal services

Stack trace dumps

🧠 Technical Reasoning
Prometheus scrapes internal metrics (HTTP handlers, Go routines, app performance).

Grafana visualizes this data. If dashboards are public, they expose a live telemetry feed of internal app behaviors — a goldmine in offensive security.

No auth = misconfiguration = high-severity info disclosure.

🛠 Tools Required
nmap, curl, wget, browser

Optional: dirb, hydra for login brute-force

🧩 Notes
Enumerate endpoint structure: /api/datasources, /api/search, /api/dashboards.

Consider lateral movement if tokens or internal services are exposed.

📌 Reminder
Everything discovered must be documented with screenshots and exploit steps for reporting and presentation.

vbnet
Copy
Edit
