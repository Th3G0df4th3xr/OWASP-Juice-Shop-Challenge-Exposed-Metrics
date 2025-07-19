📁 Method-8-Brute-Force-Endpoint-Paths-Using-Dirsearch/
markdown
Copy
Edit
# 🚨 Method-8-Brute-Force-Endpoint-Paths-Using-Dirsearch

## 🎯 Objective
Discover hidden, undocumented, or sensitive endpoints (such as `/metrics`, `/debug`, `/internal`, etc.) in the OWASP Juice Shop using brute-force enumeration via `dirsearch`. This method leverages **HTTP response analysis** and **path enumeration** to map attack surfaces that are not exposed through the UI or JavaScript.

---

## 🧪 Why This Method?

Web applications may expose internal APIs, dev/test endpoints, or monitoring interfaces unintentionally. These paths are often:
- Not linked anywhere in the UI
- Left behind by developers for diagnostics
- Used by frameworks (like Spring Boot `/actuator`, or Express debug routes)

By brute-forcing potential paths using wordlists and analyzing HTTP response codes and content-length, we can fingerprint valid endpoints.

---

## 🛠️ Requirements

- 🐍 Python 3
- 🔍 `dirsearch`: GitHub tool for brute-forcing paths  
  Install via:
  ```bash
  git clone https://github.com/maurosoria/dirsearch.git
  cd dirsearch
  pip install -r requirements.txt
🧰 Step-by-Step Execution
1. 🔥 Start OWASP Juice Shop Locally
bash
Copy
Edit
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
Confirm it's running:

bash
Copy
Edit
curl -I http://localhost:3000
2. 📂 Select Your Wordlist
Use a wordlist that targets API/admin/metrics/dev paths:

bash
Copy
Edit
cd dirsearch
Recommended:

common.txt or raft-small-words.txt

OWASP/FuzzDB lists

Custom API-focused list (api.txt with /metrics, /debug, /status, etc.)

3. 🚀 Run Dirsearch
bash
Copy
Edit
python3 dirsearch.py -u http://localhost:3000 -e json,txt,js -w wordlists/api-endpoints.txt -t 20 --random-agent --plain-text-report=results.txt
Breakdown:

-u: Target URL

-e: Extensions to try (.json, .js, etc.)

-w: Wordlist path

-t: Number of threads

--random-agent: Use a randomized User-Agent to evade basic protections

--plain-text-report: Log output to a file

🧠 What Happens Behind the Scenes?
Each path from the wordlist is appended to the base URL.

A GET request is issued.

Server responses are analyzed:

200 OK: ✅ Exists

403 Forbidden: 🚫 Exists but access blocked

401 Unauthorized: 🔐 Exists but needs auth

404 Not Found: ❌ Not found

You can further inspect valid paths using:

bash
Copy
Edit
curl http://localhost:3000/metrics
Or with Burp Repeater to inspect headers, content-type, and data leakage.

🔎 Post Enumeration
Identify endpoints that leak system-level info (e.g., Prometheus metrics, debug info)

Cross-reference with source maps or JavaScript if needed

Report all findings with:

HTTP status

Content-length

Screenshots (if required)

JSON structure or stack trace (if any)

✅ Completion Criteria
Successfully brute-force /metrics or any valid internal endpoint

Validate its exposure using direct access or tooling

Confirm “Access internal metrics” challenge solved in Juice Shop

🛡️ Mitigation Insight
Always implement endpoint access control (RBAC/ABAC)

Disable unused or development routes in production

Use deny-by-default routing logic

Implement rate-limiting to detect brute-force behavior
