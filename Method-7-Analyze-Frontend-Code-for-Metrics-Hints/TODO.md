📁 /Method-7-Analyze-Frontend-Code-for-Metrics-Hints/
markdown
Copy
Edit
# ✅ TODO - Analyze Frontend Code for Metrics Hints

## 📦 Step 1: Setup and Launch
- [ ] Launch OWASP Juice Shop using Docker
  ```bash
  docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
🧠 Step 2: Open Developer Tools
 Open browser → http://localhost:3000

 Press F12 to open DevTools

 Navigate to the Sources tab

🔍 Step 3: Source Code Exploration
 Locate and pretty-print Angular/JS bundles (main.*.js)

 Perform global search (Ctrl+Shift+F) using:

metrics

/metrics

status

http.get

HttpClient

telemetry

📈 Step 4: Trace Angular Services
 Identify relevant service or controller files

 Locate network call logic, e.g.:

ts
Copy
Edit
this.http.get('/api/internal/metrics')
 Trace hardcoded strings or concatenated paths

🧪 Step 5: Test the Endpoint
 Reconstruct and manually test the endpoint using:

Browser

Burp Suite Repeater

curl or httpie

📋 Step 6: Document the Findings
 File name and code snippet

 Endpoint path discovered

 Does it return metrics or internal data?

 Authentication required?

 Vulnerability description & screenshot

🎯 Step 7: Exploit and Validate Challenge
 Visit or invoke the endpoint to trigger the Metrics challenge toast

 Confirm successful solving of the Metrics challenge
