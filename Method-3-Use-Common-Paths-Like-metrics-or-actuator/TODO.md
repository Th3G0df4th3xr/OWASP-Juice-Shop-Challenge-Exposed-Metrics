📁 Method-3-Use-Common-Paths-Like-metrics-or-actuator/TODO.md
markdown
Copy
Edit
# ✅ TODO - Method 3: Use Common Paths Like `/metrics`, `/actuator`, etc.

This method involves scanning for known default metric endpoints that are often exposed in misconfigured web applications. These endpoints can leak sensitive operational data about the server, application, environment variables, and even credentials. Common in Java Spring Boot and other frameworks.

---

## 🧠 Objective:
Identify exposed application metrics and monitoring endpoints using known predictable paths like:
- `/metrics`
- `/actuator`
- `/actuator/health`
- `/actuator/env`
- `/manage`
- `/prometheus`
- `/debug`

---

## 🔧 Tools Required:
- `curl` (Command-line HTTP client)
- `httpx` or `ffuf` (for endpoint fuzzing)
- `Burp Suite` (optional for manual enumeration)
- `nuclei` (for automated path discovery using templates)
- Wordlist like `SecLists/Fuzzing/endpoints.txt`

---

## 🚀 Steps to Execute:

### Step 1: Recon and URL Targeting
- Identify the target domain (e.g., `http://target-domain.com`)
- Confirm the base path is accessible.

```bash
curl -I http://target-domain.com
✅ Expect: HTTP 200 OK or redirect. If not accessible, resolve DNS or use IP address.

Step 2: Check Common Metric Endpoints Manually
bash
Copy
Edit
curl -s -L http://target-domain.com/metrics
curl -s -L http://target-domain.com/actuator
curl -s -L http://target-domain.com/actuator/health
curl -s -L http://target-domain.com/manage
curl -s -L http://target-domain.com/prometheus
✅ Expect: JSON output with runtime statistics, system info, environment variables, etc.

Step 3: Automate with httpx
bash
Copy
Edit
echo "http://target-domain.com" | httpx -path /metrics,/actuator,/actuator/health,/actuator/env,/manage,/debug -status-code -title
✅ Expect: HTTP status 200 or 403 — anything other than 404 is worth inspecting.

Step 4: Use ffuf for endpoint fuzzing
bash
Copy
Edit
ffuf -u http://target-domain.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/api-endpoints.txt -mc all
✅ Expect: Any valid response (not 404), especially JSON output.

Step 5: Burp Suite Manual Check (Optional)
Open Burp > Proxy tab

Intercept a request > Send to Repeater

Modify path to /metrics or /actuator

Analyze server’s response

🧠 Strategy & Technical Reasoning
Spring Boot apps expose /actuator for health checks and metrics.

/metrics (Micrometer or Prometheus) exposes JVM metrics, memory usage, etc.

Misconfigured apps forget to secure these, leading to Information Disclosure.

Attackers can monitor:

DB connection pools

Heap memory

Thread states

Request latencies

Uptime

This is useful for post-exploitation pivoting, resource exhaustion attacks, and targeted DoS.

⚠️ Remediation Advice (For Report)
Restrict access to /metrics, /actuator, /debug using:

Network ACLs

Authentication mechanisms (e.g., Basic Auth)

Spring Security configuration

Disable unused actuator endpoints in production.

✅ Checklist
 Identify target domain

 Manual curl test for known paths

 Automate with httpx

 Run ffuf fuzzing scan

 Document responses (JSON with uptime, memory, DB)

 Screenshot valid data for report

 Propose remediation steps

📂 Output Folder Structure Suggestion
pgsql
Copy
Edit
/Method-3-Use-Common-Paths-Like-metrics-or-actuator/
│
├── screenshots/
│   └── actuator-health-exposed.png
├── raw-http/
│   └── curl-actuator-env-response.txt
├── ffuf-results.json
├── httpx-summary.txt
└── TODO.md
vbnet
Copy
Edit
