📄 Method-1-Access-Metrics-Directly-via-Known-Paths/TODO.md
markdown

# TODO - Direct Metrics Access via Known Paths

## 🎯 Objective
Identify and exploit unauthenticated access to sensitive metrics endpoints commonly exposed by web applications (e.g., `/metrics`, `/actuator`, `/actuator/metrics`) using direct HTTP request techniques.

---

## 🔧 Step-by-Step Instructions

### [ ] 1. Understand the Target Application Architecture
- **Why?** Juice Shop is a Node.js-based web application; depending on deployment configuration, it may expose debug, telemetry, or monitoring endpoints.
- **How?** These endpoints are often used by Prometheus, Spring Boot Actuator, or Node monitoring modules like `express-prom-bundle`.

---

### [ ] 2. Launch the OWASP Juice Shop Container
```bash
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
What’s Happening? This spins up a local Docker container exposing Juice Shop on localhost:3000.

Expected Output: A container ID and a running service accessible via browser or terminal at http://localhost:3000.

[ ] 3. Probe for Common Metrics Endpoints Using curl
bash
Copy
Edit
curl -I http://localhost:3000/metrics
curl -I http://localhost:3000/actuator
curl -I http://localhost:3000/actuator/metrics
Explanation:

-I fetches only HTTP headers to detect existence.

These endpoints are often hardcoded in Java Spring Boot or Node telemetry setups.

Expected Result: HTTP/1.1 200 OK if accessible; 404 or 403 otherwise.

Back-End Mechanism: The app might be exposing Prometheus metrics or JSON data due to a misconfigured monitoring setup.

[ ] 4. Investigate Response Body for /metrics or Similar
bash
Copy
Edit
curl http://localhost:3000/metrics
What to Look For:

Key-value telemetry (http_requests_total, memory usage, garbage collection stats).

Indicators like stack trace leaks, internal IPs, or service version headers.

[ ] 5. Analyze Security Implication
Why This Works: Developers leave /metrics or /actuator unprotected during dev or staging and forget to restrict in production.

Impact:

Disclosure of sensitive performance counters or service internals.

Potential for chaining with SSRF or alert fatigue attacks.

Attackers can use metrics to fingerprint the app and find time-based anomalies.

[ ] 6. Document Evidence
Take screenshots of the terminal.

Save response body:

bash
Copy
Edit
curl http://localhost:3000/metrics > metrics_exposed.txt
💡 Notes
This method is highly effective when:

No authentication is enforced.

DevOps teams forget to implement middleware-based auth guards.

WAF or reverse proxies are not filtering internal debug paths.

Consider running nmap with --script http-enum for other exposed endpoints.

🔒 Remediation Strategy
Use allowlists in reverse proxies or ingress gateways.

Enforce authentication on all debug or monitoring endpoints.

Regularly audit route exposure using dynamic application security testing (DAST).

