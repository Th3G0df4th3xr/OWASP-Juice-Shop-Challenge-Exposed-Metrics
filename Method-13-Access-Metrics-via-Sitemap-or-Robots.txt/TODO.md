📁 /Method-13-Access-Metrics-via-Sitemap-or-Robots.txt/TODO.md

markdown
Copy
Edit
# ✅ TODO - Method 13: Access Metrics via Sitemap or robots.txt

## 🎯 Objective:
Identify sensitive or misconfigured paths (including metrics endpoints) by analyzing `robots.txt` and `sitemap.xml`. These files are often exposed and can inadvertently reveal hidden directories, developer endpoints, or monitoring tools (like `/metrics`, `/prometheus`, etc.).

---

## 🛠️ Step-by-Step Technical Tasks

### [ ] 1. Discover `robots.txt` and `sitemap.xml`
- 🔧 Use a browser or CLI to visit:
  - `http(s)://<target>/robots.txt`
  - `http(s)://<target>/sitemap.xml`
- 🔍 **Tools**:
  - `curl http://<target>/robots.txt`
  - `curl http://<target>/sitemap.xml`
  - **Burp Suite Repeater/Intruder** (optional)
- 🎯 **Goal**: Spot disallowed or indexed endpoints (like `/admin/metrics`, `/monitoring`, `/private`, `/grafana/`, `/prometheus`, `/debug`).

---

### [ ] 2. Analyze and Extract Sensitive Paths
- 📌 Look for lines like:
Disallow: /metrics
Disallow: /internal-status

yaml
Copy
Edit
- These may indicate internal monitoring tools or APIs.
- 📌 In sitemap, watch for full URLs pointing to unindexed but active monitoring pages.

---

### [ ] 3. Attempt Access to Sensitive Paths
- 🖥️ Access endpoints found from `robots.txt` or `sitemap.xml`:
- Visit them via browser.
- Use `curl -v http://<target>/<path>` to observe headers, cookies, redirects.
- 🔍 Observe if endpoints reveal:
- Raw metrics (e.g., Prometheus)
- Dashboard interfaces
- Version info, error logs, runtime stats

---

### [ ] 4. Record All Findings
- ✅ Document:
- Exact URLs discovered
- What data they expose
- Response status (200 OK, 403 Forbidden, etc.)
- Whether authentication was required or bypassed

---

### [ ] 5. Evaluate Risk & Exploitability
- 🚨 Check if exposed data can:
- Aid in lateral movement
- Reveal infrastructure or application info (e.g., service names, ports)
- Allow chaining with SSRF, IDOR, or other vulnerabilities

---

## ⚙️ Optional Enhancements
- [ ] Use automated tools like **dirsearch**, **gobuster**, or **feroxbuster** to brute-force around found paths.
- [ ] Combine this with **BurpSuite Passive Scanner** for automatic endpoint fingerprinting.
- [ ] Run **Nikto** to spot common exposed files:  
`nikto -h http://<target>`

---

## ⚠️ Strategy Behind This Method:
- `robots.txt` is meant to control crawler indexing but often unintentionally reveals **admin**, **monitoring**, or **development** endpoints.
- `sitemap.xml` can list **all accessible endpoints**, including those not meant for user access.
- These files are unauthenticated and **public by design**, making them **low-noise, high-value** targets for OSINT-based reconnaissance.

---
