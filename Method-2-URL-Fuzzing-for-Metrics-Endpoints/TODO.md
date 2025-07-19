📁 /Method-2-URL-Fuzzing-for-Metrics-Endpoints/TODO.md
markdown
Copy
Edit
# ✅ TODO - Method 2: URL Fuzzing for Metrics Endpoints

## 🔧 Tool Setup
- [ ] Install `ffuf` / `gobuster` / `dirsearch` (if not already)
- [ ] Download latest `SecLists` repository or targeted wordlists
- [ ] Create a custom wordlist for metrics endpoints

## 📂 Recon and Scope Validation
- [ ] Confirm target domain/subdomain scope
- [ ] Check for redirects, wildcard DNS, or custom 404s

## 🚀 Fuzzing Execution
- [ ] Run initial `ffuf` scan with basic wordlist
- [ ] Run second pass with focused `metrics-fuzz.txt` wordlist
- [ ] Log status codes (200, 403, 401, 302)

## 🔍 Manual Verification
- [ ] Manually visit discovered endpoints
- [ ] Use `curl` or `httpie` to inspect HTTP responses
- [ ] Look for Prometheus/OpenMetrics or actuator signatures

## 🧠 Analyze Findings
- [ ] Document endpoints leaking:
  - Build version
  - Internal metrics
  - Debug info
- [ ] Flag sensitive or unauthenticated access points
- [ ] Save full HTTP responses for reporting

## 🛡️ Report Preparation
- [ ] Categorize each endpoint by risk level
- [ ] Add PoC screenshots or `curl` output
- [ ] Include mitigation steps:
  - Disable public metrics
  - Add authentication
  - Use IP whitelisting or firewall

## 🧼 Cleanup
- [ ] Delete any scan artifacts from system (optional)
- [ ] Export result logs to `./Results/` folder

---

## ⏱️ Priority Tags

- [ ] 🟥 High: `/metrics`, `/actuator`, `/debug`, `/prometheus`
- [ ] 🟧 Medium: `/status`, `/health`, `/livez`, `/readyz`
- [ ] 🟨 Low: Any custom or non-responsive metrics-looking paths

---

## 🗂️ Output Directory Structure

/Method-2-URL-Fuzzing-for-Metrics-Endpoints/
├── README.md
├── TODO.md
├── Results/
│ ├── ffuf-scan.json
│ ├── verified-endpoints.txt
│ └── curl-dumps/

Copy
Edit
