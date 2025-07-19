📋 Method-11-SSRF-to-Internal-Metrics-Endpoints/TODO.md
markdown
Copy
Edit
# ✅ TODO - SSRF to Access Internal Metrics Endpoints

## 🎯 Objective
Exploit SSRF vulnerabilities to access internal monitoring dashboards (Prometheus/Grafana) that expose telemetry data via `localhost`.

---

## 🧪 [ ] Identify Potential SSRF Injection Points
- [ ] Explore all areas in OWASP Juice Shop where user-supplied URLs are fetched by the server.
  - [ ] Common candidates:
    - **Image upload URL** (`/rest/products/<id>/image`)
    - **PDF generators**
    - **Contact forms with external fetches**

🧠 **Why?** These features accept a URL and cause the server to issue outbound requests — a vector for SSRF.

---

## 🔍 [ ] Send Controlled Internal Requests
- [ ] Modify intercepted request via **Burp Suite** or terminal tool:
    ```http
    GET /?url=http://127.0.0.1:9090/metrics HTTP/1.1
    Host: <target-ip>
    ```
- [ ] Repeat the process for:
    - `http://127.0.0.1:3000`
    - `http://localhost:3000/api/dashboards`
    - `http://127.0.0.1:9100/metrics`

🧠 **Backend Logic:** SSRF occurs when server makes a backend `curl` or `requests.get()` call without sanitizing the URL. It might fetch internal content not normally exposed to external users.

---

## 📈 [ ] Analyze the Response
- [ ] Look for:
    - `# TYPE` and `# HELP` lines (Prometheus metric headers)
    - JSON dashboard schema (Grafana)
    - Grafana login HTML pages
    - Response headers like `Server: Prometheus` or `Grafana`

🧠 **Why this matters:** Successful SSRF allows lateral movement to internal dashboards. Grafana may leak tokens or session info. Prometheus leaks system stats, uptime, target status.

---

## 🛠️ [ ] Automate and Enumerate
- [ ] Use `curl`, `ffuf`, or `httpx` to scan localhost ports:
    ```bash
    for port in {3000,9090,9100,8080}; do curl -s http://localhost:$port/metrics; done
    ```

🧠 **Strategy:** Identify which internal ports are exposed via SSRF. Use this to widen attack surface.

---

## 🔄 [ ] Test for Redirects or Filtering
- [ ] Check if server blocks `127.0.0.1` or `localhost`
- [ ] Try:
    - `http://[::1]` (IPv6 localhost)
    - DNS rebinding techniques if needed
    - Open redirects as SSRF wrappers

---

## 💥 [ ] Document Impact
- [ ] Screenshot or copy exposed metrics or JSON data
- [ ] Highlight sensitive information like:
    - `process_cpu_seconds_total`
    - `up{job="node"}`
    - JSON fields in Grafana like `"dashboard": {...}`

---

## 🧯 [ ] Add Remediation Suggestions
- Block internal IPs in SSRF handlers
- Enforce strict allowlist-based URL fetching
- Disable unused internal endpoints
