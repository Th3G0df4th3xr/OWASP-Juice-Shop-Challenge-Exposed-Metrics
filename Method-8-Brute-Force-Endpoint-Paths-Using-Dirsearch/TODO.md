✅ TODO - Brute Force Endpoint Paths Using Dirsearch
markdown
Copy
Edit
# 🔍 TODO - Brute Force Endpoint Paths Using Dirsearch

## 🛠️ Objective:
Use `dirsearch` to brute-force and uncover hidden or undocumented metric-related endpoints in the web application. This can reveal admin panels, debug interfaces, monitoring APIs, or internal metrics dashboards not publicly linked.

---

## 🔧 Setup and Requirements:
- [ ] Install `dirsearch` (if not already installed):
  ```bash
  git clone https://github.com/maurosoria/dirsearch.git
  cd dirsearch
  pip install -r requirements.txt
 Confirm target application's base URL (e.g., http://localhost:3000/ for Juice Shop)

 Choose a suitable wordlist (common choices: directory-list-2.3-medium.txt or custom metrics-focused wordlist)

🚀 Tasks to Execute:
🔹 1. Run Basic Scan
 Execute basic brute-force:

bash
Copy
Edit
python3 dirsearch.py -u http://localhost:3000/ -e all
 Monitor for endpoints such as:

/metrics

/stats

/monitor

/debug

/health

/internal/

/actuator/

🔹 2. Use Custom Wordlist
 Build or use a custom metrics-focused wordlist:

bash
Copy
Edit
python3 dirsearch.py -u http://localhost:3000/ -w wordlists/metrics.txt -e all
🔹 3. Enable Recursive Search
 Find nested hidden paths:

bash
Copy
Edit
python3 dirsearch.py -u http://localhost:3000/ -r
🔹 4. Analyze HTTP Response Codes
 Focus on status codes:

200: Found

301/302: Redirect

403: Forbidden (still valuable)

500: Internal Error (often an indicator)

🔹 5. Document All Findings
 Note discovered paths that relate to:

Monitoring services

Dashboard UIs

JSON APIs exposing system internals

 Capture screenshots, headers, and response body

🧠 Analysis Strategy:
 Review discovered endpoints manually

 Attempt access with unauthenticated and authenticated sessions

 Cross-reference with browser DevTools and JavaScript source

🛡️ Post-Findings Action:
 Report any sensitive or dangerous endpoints (especially if authentication is missing)

 Suggest hardening via:

Auth mechanisms

Restricting internal APIs

Removing unnecessary files

Rate-limiting brute-force detection

📂 Artifacts to Save:
 Output logs from dirsearch

 Screenshots of successful endpoint access

 Notes on potential security implications

📌 Optional:
 Try other tools like ffuf or gobuster for comparison

 Run in different User-Agent headers to bypass WAFs
