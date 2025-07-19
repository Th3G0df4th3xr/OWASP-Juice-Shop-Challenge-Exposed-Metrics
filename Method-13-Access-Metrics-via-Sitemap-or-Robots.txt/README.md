📂 Method-13-Access-Metrics-via-Sitemap-or-Robots.txt/README.md
markdown
Copy
Edit
# 📡 Method 13: Access Metrics via `sitemap.xml` or `robots.txt`

## 🎯 Objective:
Leverage publicly accessible discovery files (`robots.txt`, `sitemap.xml`) to uncover **internal telemetry** or **metrics endpoints** inadvertently exposed by the application, such as Prometheus or custom `/metrics` URLs. These files are used primarily by **web crawlers** and **search engine bots**, but are often forgotten or misconfigured, leaking sensitive paths.

---

## 🔧 Pre-requisites:

- ✅ OWASP Juice Shop running (local or remote)
- ✅ Browser (with DevTools) OR Terminal with tools: `curl`, `wget`, `grep`, `sed`
- ✅ Optional: Use **Burp Suite** or **DirBuster** to enhance enumeration
- ✅ Basic understanding of HTTP, web crawling directives, and endpoint fingerprinting

---

## ⚙️ Step-by-Step Execution:

---

### 🔹 Step 1: Start Juice Shop (If Local)

```bash
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
🔍 Explanation:

This launches the OWASP Juice Shop container on port 3000.

-d: detached mode

Prometheus-style or Express.js-based internal metrics may be active but hidden.

🔹 Step 2: Fetch the robots.txt and sitemap.xml
✅ Manual via Browser:
Open http://localhost:3000/robots.txt

Open http://localhost:3000/sitemap.xml

✅ Terminal Method:
bash
Copy
Edit
curl -s http://localhost:3000/robots.txt
bash
Copy
Edit
curl -s http://localhost:3000/sitemap.xml
🔍 Explanation:

robots.txt: Directives for search engine bots (e.g., Disallow: /admin).

sitemap.xml: Lists all discoverable pages for indexing.

If misconfigured, these may expose sensitive paths like /metrics, /internal, /debug, /config, or /logs.

🔹 Step 3: Analyze for Potential Endpoints
Example Output (robots.txt):
makefile
Copy
Edit
User-agent: *
Disallow: /rest/admin/
Disallow: /metrics
🎯 Target: /metrics is explicitly disallowed to bots, but accessible to users directly.

🔹 Step 4: Visit or Query the Endpoint
bash
Copy
Edit
curl -i http://localhost:3000/metrics
🧠 Technical Behind-the-Scenes:

curl -i: Fetches headers + body of HTTP response.

Metrics endpoints often return Prometheus-compatible format (text/plain with # HELP and # TYPE labels).

🔹 Step 5: Validate Response & Look for Sensitive Data
Look for:

Server uptime

User count

JWT expiry or auth flags

Debug flags or environment variables

If metrics contain internal info (CPU, memory, exceptions), mark this as critical information disclosure.

🧠 Strategy & Rationale
These files (robots.txt, sitemap.xml) are meant for automation, not human consumption, hence often overlooked in traditional pentests.

Developers may forget to lock down access since "they are just for Google bots".

Metrics endpoints may bypass auth middleware due to misconfigured routes or legacy dev behavior.

🛡️ Mitigation Recommendations
Serve robots.txt and sitemap.xml without sensitive paths.

Protect /metrics, /debug, etc. using auth middleware or IP whitelisting.

Use robots.txt only for non-sensitive disallow rules; never rely on it for security.

🧪 Sample Burp Intercept Flow (Optional):
Start browser → http://localhost:3000/robots.txt

Capture in Burp Proxy.

Forward to Repeater → Change URL to /metrics, /debug, etc.

Send and analyze HTTP status + body.

📸 Artifacts to Save:
Screenshot of robots.txt and exposed metrics

Full curl output of metrics endpoint

HTTP 200 response if /metrics is accessible unauthenticated

✅ Success Criteria
Discovered /metrics or related path from robots.txt or sitemap.xml

Accessed without authentication

Response contains sensitive server data or telemetry

🧠 Offensive Security Notes:
This method falls under "Unprotected sensitive files" or "Information Disclosure"

Real-world breaches (e.g., Tesla’s Kubernetes dashboard) have occurred via similar exposed paths in robots.txt

Easy to automate across domains in a bug bounty or red team engagement

vbnet
Copy
Edit
