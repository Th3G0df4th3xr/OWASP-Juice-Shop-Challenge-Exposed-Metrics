✅ TODO.md
markdown
Copy
Edit
# ✅ TODO - BurpSuite Interception Method

## 🔧 Setup Phase
- [ ] Launch OWASP Juice Shop (`docker run -d -p 3000:3000 bkimminich/juice-shop`)
- [ ] Open Burp Suite and configure the browser proxy to `127.0.0.1:8080`
- [ ] Import Burp CA certificate in the browser (for HTTPS interception)

## 🕵️ Interception and Discovery
- [ ] Login to Juice Shop as any user
- [ ] In Burp → HTTP history, filter requests by keywords:
  - `metrics`, `config`, `api`, `rest`
- [ ] Identify API endpoints that may reveal internal data

## 🧪 Testing and Manipulation
- [ ] Send suspicious requests to **Repeater**
- [ ] Manually fuzz paths like:
  - `/api/metrics`
  - `/admin/metrics`
  - `/internal/prometheus`
- [ ] Observe JSON, headers, or responses revealing telemetry

## 🎯 Goal
- [ ] Identify exposure of internal metrics, configuration, uptime, or system data
- [ ] Capture screenshot of any successful exploit

## 📸 Documentation
- [ ] Save all intercepted payloads and responses
- [ ] Log endpoint names and response codes
- [ ] Record mitigation ideas (e.g., auth checks, rate limiting)

## 🚫 Cleanup (optional)
- [ ] Remove Burp certificate from browser after test
