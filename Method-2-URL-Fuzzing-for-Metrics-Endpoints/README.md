📁 /Method-2-URL-Fuzzing-for-Metrics-Endpoints/README.md
markdown
Copy
Edit
# 🔎 Method 2: URL Fuzzing for Metrics Endpoints

## 🧠 Objective
Identify misconfigured or unintentionally exposed metrics endpoints by fuzzing common and uncommon paths on the target web server. This approach is aimed at uncovering telemetry, diagnostics, and internal health-check endpoints that may leak sensitive operational data such as application internals, stack traces, or live traffic metrics.

---

## 🧰 Requirements

- 🔧 Tools:
  - `ffuf`, `dirsearch`, or `gobuster`
  - Wordlists (SecLists, custom fuzz lists)
  - `curl` or `httpie` for manual verification
- 🗃️ Wordlists:
  - `SecLists/Discovery/Web-Content/common.txt`
  - Custom list: `metrics`, `debug`, `health`, `status`, etc.

---

## 🚀 Execution Steps

### 1. 🧭 Initial Recon
- Confirm base domain or subdomain scope.
- Check for wildcard DNS or redirects.
- Identify common path patterns.

### 2. ⚙️ Launch Fuzzing
Example using `ffuf`:
```bash
ffuf -u https://target-site.com/FUZZ -w wordlist.txt -mc 200,302,403 -t 100 -o found.json
Use specialized lists:

bash
Copy
Edit
cat > metrics-fuzz.txt <<EOF
metrics
actuator/metrics
prometheus
health
status
debug
_env
livez
readyz
EOF
3. 🔍 Analyze Responses
Look for:

Prometheus/OpenMetrics-style outputs

heap_used, threads, gc_time, jvm_uptime, etc.

JSON, plain text, or HTML pages

Check for accessible endpoints like:

/metrics

/actuator/prometheus

/admin/metrics

/monitoring

4. 🧪 Manual Exploration
Verify interesting findings:

bash
Copy
Edit
curl -s https://target-site.com/metrics | less
Look for stack information, JVM metrics, build version leaks, etc.

🎯 Goals of Discovery
🔓 Extract insights about:

Infrastructure telemetry

Application framework (e.g., Spring Boot Actuator)

Internal-only endpoints exposed to public

🔁 Identify SSRF pivot points via embedded URLs

🔐 Flag misconfigurations: Prometheus exporters, Grafana dashboards

🧼 Post-Enumeration
Save all findings in structured format:

Endpoint

Response code

Response type

Data content (partial dump)

Report potential security impact:

Environment info leak

DoS risk from verbose metrics

Internal service exposure

📚 References
🔗 OWASP API Security - API7:2023 - Security Misconfiguration

🧰 SecLists - Fuzzing Wordlists

🔧 ffuf - Fast Web Fuzzer

📊 Prometheus Exporter Defaults

🧠 Notes
Always verify response codes: 200 OK, 403 Forbidden (interesting), 401 Unauthorized (possible protected metrics)

Take note of CSP headers; they may correlate with metric endpoint leaks

This method pairs well with Method-1 and Method-18 for chaining enumeration techniques

⚠️ Disclaimer
Use only on targets you have explicit permission to test. Misuse may lead to legal consequences or disruption of services.
