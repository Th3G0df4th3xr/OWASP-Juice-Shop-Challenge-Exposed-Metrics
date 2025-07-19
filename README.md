# OWASP-Juice-Shop-Exposed-Metrics-Challenge
# 🧠 OWASP Juice Shop Exposed Metrics Challenge

## 🎯 Objective

This challenge aims to simulate a real-world scenario in which **internal application metrics endpoints (e.g., Prometheus, Grafana, Spring Boot Actuator)** are **exposed over the public-facing attack surface**, risking **sensitive performance data leakage**, **internal host enumeration**, or even **command execution** in some configurations.

---

## 📂 Repository Structure

OWASP-Juice-Shop-Exposed-Metrics-Challenge/
├── Method-1-Access-Metrics-Directly-via-Known-Paths/
├── Method-2-URL-Fuzzing-for-Metrics-Endpoints/
├── Method-3-Use-Common-Paths-Like-metrics-or-actuator/
├── Method-4-Check-Nginx-Or-Apache-Default-Stats/
├── Method-5-Decode-Obfuscated-Links-in-JavaScript/
├── Method-6-Use-DevTools-to-Capture-Network-Traffic/
├── Method-7-Analyze-Frontend-Code-for-Metrics-Hints/
├── Method-8-Brute-Force-Endpoint-Paths-Using-Dirsearch/
├── Method-9-Use-Wappalyzer-to-Detect-Frameworks/
├── Method-10-Check-for-Open-Grafana-or-Prometheus-Dashboards/
├── Method-11-SSRF-to-Internal-Metrics-Endpoints/
├── Method-12-Use-BurpSuite-to-Intercept-API-Calls/
├── Method-13-Access-Metrics-via-Sitemap-or-Robots.txt/
├── Method-14-Exploit-Backup-Files-to-Find-Metrics-Endpoints/
├── Method-15-Check-Headers-for-Server-Exposures/
├── Method-16-Use-Shodan-to-Find-Exposed-Metrics/
├── Method-17-Analyze-SourceMap-Files-for-Metrics-Clues/
├── Method-18-Use-CSP-Reports-to-Reveal-Internal-Endpoints/
└── README.md

markdown
Copy
Edit

---

## 🔍 What Are Metrics Endpoints?

**Metrics endpoints** expose real-time information such as memory usage, thread counts, build versions, garbage collection stats, service dependencies, DB queries, etc. Examples include:

- `/metrics`
- `/actuator/health`
- `/actuator/env`
- `/prometheus`
- `/stats`

These are intended for **internal observability**, but when publicly exposed, become **goldmines for attackers.**

---

## 🛠️ Tools Used

- `curl` — Command-line tool for making raw HTTP requests
- `dirsearch` — Fast brute-forcer for files and directories on web servers
- `Burp Suite` — MITM Proxy for web application testing
- `Chrome DevTools` — For inspecting network requests
- `Wappalyzer` — Detects backend technologies
- `nmap`, `nikto` — Host/service enumeration
- `shodan` — Search engine for exposed devices/services
- `jq`, `grep`, `strings` — Terminal utilities for parsing data

---

## 🧩 Methodologies (with Strategy)

> Each method represents a **different approach an attacker might take** to uncover exposed metrics. Below is an index of all methods and a brief overview of their **offensive strategy**.

### 🔍 Method 1: Access Metrics Directly via Known Paths
- Try `/metrics`, `/actuator`, `/prometheus`, etc.
- Strategy: Leverage **developer misconfiguration** or **default exposure**.
- `curl http://<target-ip>:<port>/metrics`

### 🌐 Method 2: URL Fuzzing for Metrics Endpoints
- Use tools like `ffuf` or `dirsearch` with wordlists.
- Strategy: Fuzz endpoints using popular terms.
- Example:
  ```bash
  dirsearch -u http://<target-ip> -e html,json,txt -w common-endpoints.txt
📦 Method 3: Use Common Paths Like /metrics or /actuator
Strategy: Exploit popular framework endpoints (Spring Boot, Micrometer).

Expectation: JSON/Prometheus-formatted metrics exposed.

📈 Method 4: Check Nginx or Apache Default Stats
Look for /server-status or /nginx_status.

Strategy: Web server misconfiguration leads to traffic analysis exposure.

🔍 Method 5: Decode Obfuscated Links in JavaScript
Use DevTools or regex to search .js files for encoded endpoints.

Strategy: Developers hide internal links using Base64, URL encoding.

🧪 Method 6: Use DevTools to Capture Network Traffic
Observe API traffic to catch internal monitoring calls.

Strategy: Reverse engineer SPA front-end behavior.

🔍 Method 7: Analyze Frontend Code for Metrics Hints
Grep through codebase or DevTools Sources.

Strategy: Metrics endpoints hardcoded into UI/JS logic.

🛠️ Method 8: Brute Force Endpoint Paths Using Dirsearch
Identify hidden or unlinked metrics paths.

Wordlist: SecLists/Discovery/Web-Content/common.txt

Example:

bash
Copy
Edit
dirsearch -u http://<target-ip> -w /usr/share/seclists/Discovery/Web-Content/common.txt
🧠 Method 9: Use Wappalyzer to Detect Frameworks
Strategy: Fingerprint tech stack (e.g., Spring Boot, NodeJS)

Helps guess which metrics tools might be in use.

📊 Method 10: Check for Open Grafana or Prometheus Dashboards
Example paths:

/grafana/

/prometheus/

Strategy: Full access to dashboards allows deep monitoring access.

🌍 Method 11: SSRF to Internal Metrics Endpoints
SSRF payloads like:

http
Copy
Edit
http://127.0.0.1:9090/metrics
Strategy: Use a proxy endpoint to reach internal-only services.

🧰 Method 12: Use BurpSuite to Intercept API Calls
Inspect background API calls on button click/page load.

Strategy: Replay or modify requests to test access controls.

🧭 Method 13: Access Metrics via sitemap.xml or robots.txt
Hidden endpoints often unintentionally listed here.

Strategy: Automated discovery via crawling artifacts.

💾 Method 14: Exploit Backup Files to Find Metrics Endpoints
Access .bak, .zip, .tar.gz, etc.

Strategy: Find exported source files with internal routes.

📎 Method 15: Check Headers for Server Exposures
Look for X-Powered-By, Server, Via headers.

Strategy: Server stack leak leads to targeted enumeration.

🌐 Method 16: Use Shodan to Find Exposed Metrics
Query:

arduino
Copy
Edit
http.title:"Prometheus Time Series Collection and Processing Server"
Strategy: Global scans reveal unsecured deployments.

📁 Method 17: Analyze SourceMap Files for Metrics Clues
*.map files expose original JS code.

Strategy: Reveal internal functions, routes, and endpoints.

🧷 Method 18: Use CSP Reports to Reveal Internal Endpoints
CSP Violation reports often point to endpoints not public.

Strategy: Bypass normal route enumeration.

🧠 Offensive Strategy Summary
This challenge trains you to think like a red teamer or bug bounty hunter looking for observability pipeline leaks. The aim is to understand the intersection of monitoring systems and attack surfaces, with a focus on misconfigurations, lack of RBAC, and poor endpoint hardening.

🧪 Goal Completion Criteria
✅ Locate an exposed metrics endpoint.

✅ Dump metric data or dashboard access.

✅ Document steps taken, tools used, and outcomes.

✅ Suggest remediation steps (e.g., internal-only routing, authentication, firewall).

🔒 Remediation Guidance
Restrict metrics access to localhost/VPN.

Use auth middleware or gateway protection.

Harden CSP, remove source maps from prod.

Review robots.txt and sitemap.xml exposure.

📣 Contributors
Maintained by Th3G0df4th3xr — for educational and ethical offensive research.

🛑 This repository is for educational use only. Never perform unauthorized scans or attacks on systems you do not own or have explicit permission to test.

yaml
Copy
Edit
