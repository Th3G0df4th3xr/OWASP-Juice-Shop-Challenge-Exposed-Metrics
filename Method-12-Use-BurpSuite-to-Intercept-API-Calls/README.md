📘 Method-12-Use-BurpSuite-to-Intercept-API-Calls/README.md
markdown
Copy
Edit
# 🧰 Method-12-Use-BurpSuite-to-Intercept-API-Calls

## 🔍 Objective
Intercept and manipulate internal API calls made by the Juice Shop frontend to expose metrics-related endpoints (e.g., Prometheus, Grafana, health check APIs, or internal stats).

---

## 🧠 Overview
Modern SPAs (Single Page Applications) like OWASP Juice Shop use REST APIs heavily. Using Burp Suite allows attackers to:
- View and modify traffic between browser and backend
- Reveal hidden API endpoints
- Bypass client-side restrictions
- Replay and fuzz API calls to uncover unintended functionality

---

## ⚔️ Attack Surface
API endpoints such as:
- `/rest/metrics`
- `/api/Telemetry`
- `/internal/healthcheck`
- `/admin/dashboard-data`

These may not be visible in the browser UI but can be exposed and tested via interception.

---

## 🛠️ Tools Used
- **Burp Suite Community/Professional**
- Optional: **Browser with Burp's certificate imported**
- **OWASP Juice Shop running on `localhost` or Docker**

---

## 🚀 Steps Summary
1. Configure browser to use Burp as proxy (`127.0.0.1:8080`)
2. Open Juice Shop in the browser and log in
3. In Burp’s HTTP history, filter by `/rest`, `/api`, or `metrics`
4. Send interesting requests to **Repeater**
5. Modify requests to probe internal APIs like:
   - `/api/prometheus`
   - `/metrics`
   - `/api/config`
6. Observe any leakage of system stats or configuration

---

## 🔐 Why It Matters
Intercepting and manipulating APIs is key to:
- Finding undocumented endpoints
- Accessing hidden or internal resources
- Identifying SSRF or IDOR opportunities
- Triggering backend processing via custom-crafted input

---

## 🔒 Remediation (If Vulnerable)
- Enforce strong API authentication
- Disable unused endpoints
- Perform backend access control validation
- Log and monitor unexpected API usage patterns

---

## 📎 Related Techniques
- [Method-10-Check-for-Open-Grafana-or-Prometheus-Dashboards](../Method-10-Check-for-Open-Grafana-or-Prometheus-Dashboards)
- [Method-11-SSRF-to-Internal-Metrics-Endpoints](../Method-11-SSRF-to-Internal-Metrics-Endpoints)
