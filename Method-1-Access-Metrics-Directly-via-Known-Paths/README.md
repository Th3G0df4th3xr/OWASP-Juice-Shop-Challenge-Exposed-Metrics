📌 Objective
Access internal server metrics exposed through unauthenticated HTTP endpoints like /metrics, /actuator/metrics, or /prometheus which may leak sensitive information such as internal infrastructure stats, memory usage, database access patterns, and service health.

🧠 Offensive Strategy & Background
Modern web applications built using frameworks like Spring Boot (Java), Micronaut, or Dropwizard often expose application metrics endpoints for observability and health monitoring purposes. These are typically consumed by DevOps tools like Prometheus, Grafana, or New Relic.

These endpoints, when misconfigured (i.e., not protected behind authentication or IP restrictions), expose internal telemetry data which can be weaponized by an attacker for:

Reconnaissance (Understanding app internals like DB, memory, GC cycles)

Pivoting (Identifying injectable handlers, secrets, or open APIs)

Denial-of-Service optimization (Knowing memory limits and thresholds)

🛠️ Prerequisites
Juice Shop running on localhost or remote (default port: 3000)

Kali Linux or any Linux shell with curl, grep, and jq installed

Browser access to http://localhost:3000 or your deployed instance

🧪 Step-by-Step Method
✅ Step 1: Identify Common Metric Endpoints
These are well-known paths hardcoded in popular frameworks:

bash
Copy
Edit
/metrics
/actuator/metrics
/prometheus
/stats
/debug/metrics
These endpoints are potential entry points. We’ll brute-force these manually using curl.

🖥️ Step 2: Use curl to Test for Open Metric Endpoints
bash
Copy
Edit
curl -I http://localhost:3000/metrics
🔍 Explanation:
curl -I sends a HEAD request.

We’re testing if this path returns HTTP 200 OK or 403/404/500.

📌 Expected Output:
If endpoint exists, you'll see:

http
Copy
Edit
HTTP/1.1 200 OK
Content-Type: text/plain
If not:

http
Copy
Edit
HTTP/1.1 404 Not Found
Repeat this for other endpoints:

bash
Copy
Edit
curl http://localhost:3000/actuator/metrics
curl http://localhost:3000/prometheus
✅ Step 3: Inspect Raw Output for Sensitive Data
If an endpoint responds with a 200 OK, try to extract key system internals:

bash
Copy
Edit
curl http://localhost:3000/metrics | less
Look for fields like:

jvm_memory_used_bytes

process_cpu_usage

system_load_average

requests_total

db_connections

http_server_requests_seconds_max

📖 What’s Happening in Backend?
The application is exposing metrics via a Metrics Registry. For instance:

In Spring Boot: via Micrometer + Actuator

In Node.js apps: via Prom-client or StatsD exporters

The data is in plaintext or Prometheus exposition format, designed for machines but readable by attackers.

🧬 Step 4: Use jq or grep to Extract Key Details
Example:

bash
Copy
Edit
curl http://localhost:3000/metrics | grep -i cpu
Or format JSON if needed:

bash
Copy
Edit
curl http://localhost:3000/actuator/metrics | jq
🧠 Why This Works
Developers often forget to:

Disable actuator/metrics in production

Secure with auth tokens, IP whitelisting, or reverse proxy auth

Modify application.properties or actuator settings

Hence, attackers exploit this misconfiguration to perform unauthenticated telemetry reconnaissance.

🔐 Real-World Exploit Scenarios
Targeted DoS:
Knowing GC patterns and CPU pressure allows precision DoS via memory exhaustion.

API Enumeration:
Some metrics expose URL patterns or handlers revealing the internal API structure.

Credential Leakage:
Occasionally, misconfigured debug metrics leak tokens or headers.

🧼 Mitigation (For Blue Teams)
Always protect metrics endpoints behind:

IP-based firewall rules (iptables, security groups)

Authentication headers (Bearer Token or Basic Auth)

Reverse proxies (nginx or Apache)

In Spring Boot:

properties
Copy
Edit
management.endpoints.web.exposure.include=none


| Step | Command                                                               | Purpose                              |              |
| ---- | --------------------------------------------------------------------- | ------------------------------------ | ------------ |
| 1    | `curl -I http://localhost:3000/metrics`                               | Probe endpoint                       |              |
| 2    | \`curl [http://localhost:3000/metrics](http://localhost:3000/metrics) | less\`                               | Read content |
| 3    | `grep`, `jq`                                                          | Extract keywords                     |              |
| 4    | Manual Analysis                                                       | Identify DB, memory, and system data |              |

