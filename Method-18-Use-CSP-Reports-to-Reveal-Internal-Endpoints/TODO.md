📁 /Method-18-Use-CSP-Reports-to-Reveal-Internal-Endpoints/TODO.md
markdown
Copy
Edit
# ✅ TODO - Method 18: Use CSP Reports to Reveal Internal Endpoints

## 🧠 Objective
Leverage Content Security Policy (CSP) violation reports as an intelligence vector to uncover internal URLs, endpoints, or domains not meant to be exposed externally. This technique aims to exploit misconfigured or verbose CSP headers that send reporting data to attacker-controlled infrastructure.

---

## 🛠️ Preparation

- [ ] Setup CSP report collector server (Python or Node.js listener)
- [ ] Register a reporting endpoint (e.g., `https://yourattacker.report/csp`)
- [ ] Configure logging and data parsing on your CSP collector
- [ ] Setup and confirm DNS resolution of the attacker domain
- [ ] Ensure HTTPS with valid certs (Let's Encrypt or self-signed)

---

## 🔍 Target Reconnaissance

- [ ] Inspect site response headers using:
  ```bash
  curl -I https://target-site.com
Look for headers like:

Content-Security-Policy-Report-Only

report-uri

report-to

 Confirm if CSP reports are being actively generated

 Identify any inline script restrictions or disallowed resource types

🧪 Inject Malicious CSP Triggers
 Craft HTML injection that violates CSP policies:

html
Copy
Edit
<img src="https://evil.com/steal.png" />
 Look for stored XSS or reflected injection points to trigger CSP reports

 Test payloads in locations such as:

Search bars

Comment sections

Profile descriptions

🧬 Advanced Payload Engineering
 Use techniques to trigger CSP violations silently:

Blocked scripts or style URIs

object-src violations

img-src pointing to 127.0.0.1 or internal IPs

 Embed payloads inside browser extensions (if applicable)

📥 Receive and Analyze Reports
 Log incoming JSON reports using your custom server

 Parse fields:

blocked-uri

document-uri

source-file

script-sample

 Extract and log any:

Internal endpoints (*.internal.local)

IPs in 10.x.x.x, 172.16.x.x, 192.168.x.x

Non-production hostnames

Sensitive file paths (/admin, /metrics, /debug)

📊 Post-Exploitation Intelligence
 Correlate leaked endpoints with your active recon list

 Try accessing internal endpoints from external network

 Use SSRF, DNS rebinding, or VPN tunneling to pivot

 Enumerate leaked ports or technologies

🛡️ Defensive Considerations
 Check for Content-Security-Policy best practices

 Use strict default-src 'self' and disable report-uri for public domains

 Avoid using overly verbose reporting in production

📚 Reference Scripts and Tools
 csp_report_collector.py

 nginx_csp_proxy.conf

 analyze_csp_logs.py

🧼 Cleanup
 Take down listener once data is collected

 Secure attacker-controlled domains

 Sanitize any injected payloads (if pentesting)

🏁 Final Deliverables
 Screenshots of CSP reports received

 Full report of leaked endpoints

 Technical breakdown of what triggered each CSP violation

 Suggestions for CSP hardening for target org
