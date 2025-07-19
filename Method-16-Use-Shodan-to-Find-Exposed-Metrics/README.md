📁 /Method-16-Use-Shodan-to-Find-Exposed-Metrics/README.md
markdown
Copy
Edit
# 🔎 Method-16: Use Shodan to Find Exposed Metrics Endpoints

## 🧠 Objective:
Leverage **Shodan**, the world’s first search engine for Internet-connected devices, to **discover exposed server metrics endpoints**, particularly those related to **Prometheus**, **Grafana**, **Kubernetes**, or other telemetry systems unintentionally exposed to the public internet.

---

## 🧰 Tools Used:
- 🔍 **Shodan.io**
- 🧠 Browser + Shodan queries
- 🧪 Optional: Python Shodan API client (for automation)

---

## 🌐 Step-by-Step Technical Guide:

### 🧩 Step 1: Understand the Attack Surface

**Telemetry & Metrics Endpoints** (like Prometheus `/metrics`, Grafana dashboards, etc.) expose sensitive internal information:
- CPU usage, memory, internal hostnames
- IPs, ports, service names
- Authentication failures, error logs
- Even internal service credentials (in misconfigured setups)

🔴 **Goal**: Use Shodan to **find such endpoints** exposed directly to the internet without proper authentication.

---

### 🔐 Step 2: Understand What Shodan Does

**Shodan** continuously scans the entire IPv4 address space using:
- Custom-built scanners
- Banner grabbing via protocols (HTTP, FTP, SSH, SNMP, etc.)
- SSL certificate data collection
- Header and metadata indexing

It stores metadata like:
- Open ports
- Response banners
- HTTP response headers
- Technology stack
- Title of pages
- HTML content for specific endpoints

---

### 🔎 Step 3: Manual Recon via Web Interface

1. 🔗 Go to [https://www.shodan.io](https://www.shodan.io)
2. In the **search bar**, use the following queries:

#### 📌 Search Queries:
```bash
http.title:"Prometheus Time Series Collection and Processing Server"
http.title:"Grafana"
http.favicon.hash:-1399432067  # Common hash for exposed Prometheus
port:9090                      # Default Prometheus port
port:3000                      # Default Grafana port
"X-Prometheus-Scrape-Timeout-Seconds"
"Metrics endpoint"
📚 Explanation of Queries:

http.title — searches by the page title that appears in <title> HTML tag

port:9090 — Prometheus default port

favicon.hash — Shodan hashes favicon icons and allows matching known apps via this

"X-Prometheus-..." — header present in raw Prometheus responses

🔄 Shodan will return a list of live IPs with banners matching these queries.

🖱️ Click any result → You'll see:

IP address

Open ports

Technology stack

Location info

Raw HTTP response

Screenshot (if available)

🧪 Step 4: Validate the Endpoint
In Your Terminal:
bash
Copy
Edit
curl -s http://<IP>:9090/metrics
Expected Output:
Plaintext response with thousands of lines like:

bash
Copy
Edit
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
go_gc_duration_seconds{quantile="0"} 0.000065
...
This confirms it's a Prometheus endpoint exposing raw metrics — a critical data leak.

🐍 Step 5: Automate Recon (Optional - Python Shodan API)
📦 Install Python API
bash
Copy
Edit
pip install shodan
🔑 Get API Key
Sign in at https://account.shodan.io and grab your key.

🧪 Script:
python
Copy
Edit
import shodan

API_KEY = "YOUR_API_KEY"
api = shodan.Shodan(API_KEY)

results = api.search('http.title:"Prometheus Time Series Collection and Processing Server"')

for result in results['matches']:
    print(f"IP: {result['ip_str']}")
    print(f"Port: {result['port']}")
    print(f"Org: {result.get('org', 'n/a')}")
    print(f"Data:\n{result['data']}")
    print("="*50)
🚨 Why This Works (Technical Strategy):
Many DevOps teams deploy Prometheus, Grafana, and other telemetry solutions without setting up firewall rules or authentication.

These systems expose:

Service names, job names, ports

Hostnames and IPs of internal pods or services

CPU, memory, disk info of internal nodes

An attacker can pivot from this telemetry to:

Exploit known vulnerabilities

Conduct lateral movement

Map internal infrastructure

Real-world cases: Several CVEs have been identified in misconfigured Grafana dashboards that allow LFI, unauthenticated access, or RCE.

🛡️ Remediation Recommendations:
🔒 Always restrict Prometheus & Grafana to internal-only networks.

🔑 Enable authentication on all telemetry dashboards.

🧱 Use firewall rules to allow access only from specific IPs.

🚫 Block public access on ports 9090, 3000, and similar.

✅ Outcome
By following this method:

You identify exposed telemetry systems

Extract valuable metadata for exploitation planning

Build a solid reconnaissance base for vulnerability chaining

📚 References
https://shodan.io

Prometheus Metrics Format

CVE List for Grafana

vbnet
Copy
Edit
