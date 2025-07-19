📁 /Method-3-Use-Common-Paths-Like-metrics-or-actuator/
markdown
Copy
Edit
# 📡 Method 3 - Use Common Paths Like `/metrics`, `/actuator`

## 📌 Objective

To identify and access internal telemetry, debugging, or monitoring endpoints by directly querying **commonly exposed paths** such as `/metrics`, `/actuator`, `/health`, etc. This method leverages **predictable URLs** often left exposed in web apps and microservices—particularly those using frameworks like **Spring Boot**, **Micronaut**, **Prometheus**, or **Kubernetes-native services**.

---

## ⚙️ Why This Works

Developers often use frameworks (e.g., Spring Boot) that expose **monitoring endpoints** for operational visibility. These are:
- Automatically added under paths like `/actuator`, `/metrics`, `/health`.
- Designed for **internal devops use** but often forgotten during deployment and security hardening.
- If left **unauthenticated or publicly exposed**, these endpoints can leak:
  - JVM stats, heap usage, environment vars
  - Active threads, HTTP mappings, database pools
  - System health status and build info
  - Entire dependency tree of microservices

**Why we're doing this:**
> Discovering such paths reveals sensitive telemetry without authentication, which is useful for chaining with other attacks like **RCE**, **Info Disclosure**, or **Privilege Escalation**.

---

## 🧠 Strategy Overview

1. Manual + Automated probing of known telemetry paths.
2. Validate each hit with response inspection.
3. Analyze responses for information leaks or misconfigurations.
4. Document and escalate any path with non-public or critical info.

---

## 🛠️ Prerequisites

Install the following tools (if not already available):

```bash
sudo apt install curl httpie
sudo apt install gobuster ffuf
Get the latest wordlists:

bash
Copy
Edit
git clone https://github.com/danielmiessler/SecLists.git
🧪 Step-by-Step Execution
🔍 Step 1: Identify Target
Assume the target is:
http://target-domain.com
or
http://<target-ip>:<port>

You can also use nmap or whatweb to find the running tech stack:

bash
Copy
Edit
whatweb http://target-domain.com
Check for signs of:

Java stack → Try /actuator

Prometheus/Grafana → Try /metrics

K8s-based → Try /healthz, /readyz, /livez

🚀 Step 2: Manual Probing of Known Paths
Use curl or httpie to test each path manually.

bash
Copy
Edit
curl -i http://target-domain.com/actuator
✅ Expected Output:
HTTP 200 with JSON keys like {"_links":{"self":..., {"status":"UP"}, etc.

Try others:

bash
Copy
Edit
curl -i http://target-domain.com/metrics
curl -i http://target-domain.com/actuator/health
curl -i http://target-domain.com/actuator/env
curl -i http://target-domain.com/actuator/beans
curl -i http://target-domain.com/actuator/mappings
📖 Backend Technicals:

/metrics → Exposes internal app metrics (JVM, threads, DB connections).

/env → Leaks environment variables, dangerous if secrets are in ENV.

/beans → Spring context beans—full dependency injection tree.

/mappings → Endpoint-to-controller mappings.

If these are unauthenticated, this is a serious Info Disclosure vulnerability.

⚡ Step 3: Automate with Wordlist
Use ffuf for fast discovery:

bash
Copy
Edit
ffuf -u http://target-domain.com/FUZZ -w ./SecLists/Discovery/Web-Content/common.txt -fc 404 -t 100 -o metrics-fuzz.json
Explanation:

-fc 404 filters common 404s

-t 100 sets concurrency

-o outputs to JSON for analysis

-w uses wordlist with entries like:

metrics

actuator

prometheus

debug

livez, readyz, health

🔎 Step 4: Deep Inspection of Response
For each discovered endpoint:

bash
Copy
Edit
curl http://target-domain.com/actuator/env | jq
Look for:

JAVA_HOME, DB_USER, DB_PASSWORD

Environment secrets (AWS_KEYS, SMTP creds)

Debug flags like spring.devtools.enabled=true

📊 Example Critical Outputs to Look For
Endpoint	What It Leaks	Risk Level
/actuator/env	Env vars incl. credentials	🔥 High
/metrics	Heap, DB pool, thread info	⚠️ Medium
/actuator/beans	Internal app structure (spring beans)	⚠️ Medium
/actuator/mappings	All registered endpoints with handlers	⚠️ Medium
/actuator/loggers	Logging configuration, log level abuse	⚠️ Medium

🔐 Mitigation Recommendations
If such paths are exposed:

🔒 Add Authentication (Basic Auth, JWT)

🔄 Disable endpoints not needed in production

🔥 Use management.endpoints.web.exposure.exclude=* in Spring config

🧱 Restrict access via WAF or IP allowlist

📁 Suggested Folder Structure
sql
Copy
Edit
/Method-3-Use-Common-Paths-Like-metrics-or-actuator/
├── README.md
├── TODO.md
├── Results/
│   ├── curl-outputs/
│   ├── ffuf-scan.json
│   └── sensitive-data-samples.txt
🧠 Final Notes
This method is quiet, doesn’t generate noisy traffic like brute force or scanner fuzzing, and is often enough to reveal internally exposed telemetry endpoints. In real-world engagements, such exposures can lead to full application compromise through chaining with RCE or SSRF if exploitable paths (like Jolokia or actuator shell) exist.

🧩 Next Steps: Try chaining with:

Jolokia exposure

Spring Cloud Function RCE

Log4Shell callbacks

kotlin
Copy
Edit
