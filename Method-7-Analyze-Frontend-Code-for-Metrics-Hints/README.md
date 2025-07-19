Here’s a highly technical and step-by-step README for:

📁 /Method-7-Analyze-Frontend-Code-for-Metrics-Hints/
🔍 Objective
To analyze the frontend source code (HTML, JavaScript, frameworks like Angular/React) of the OWASP Juice Shop to identify hardcoded or hidden references to internal metrics endpoints or functions that leak sensitive internal telemetry data.

📖 Why this method?
Modern Single Page Applications (SPAs) rely heavily on frontend JavaScript frameworks (like Angular in Juice Shop). Sometimes, developers accidentally expose sensitive API endpoints by referencing them in:

JavaScript files

Angular services

Hidden form inputs

Client-side logic for metrics aggregation (e.g., /api/metrics, /rest/status, etc.)

By reverse engineering the source files, we can uncover such references even if no UI interaction exposes them directly.

🧪 Step-by-Step Guide
1. 🔓 Accessing the Web Application
Start your local instance:

bash
Copy
Edit
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
Visit http://localhost:3000 in your browser.

2. 🧠 Understanding the Source Structure
Juice Shop is written in Angular.

JS files are minified during production. Still, Angular keeps a modular structure.

3. 🧰 Open DevTools to View JS Sources
Press F12 → Go to Sources tab.

Navigate to the Webpack/Angular Bundles:

These usually appear like: main.*.js, polyfills.*.js, etc.

Tip: Use Pretty Print {} to make the minified JS readable.

4. 🔍 Searching for Clues
Use Ctrl+Shift+F to search across all files:

Search keywords:

bash
Copy
Edit
metrics
/metrics
/api/
/actuator
status
telemetry
xhr
fetch
http.get
You may find lines like:

js
Copy
Edit
this.http.get('/api/metrics')
or

ts
Copy
Edit
return this.httpClient.get(METRICS_ENDPOINT)
5. 🧵 Trace to Angular Services
Angular apps define services to encapsulate logic:

You may find a service like:

ts
Copy
Edit
metricsService.getMetrics()
Trace its @Injectable() class to see actual endpoint calls.

6. 🪛 Reconstruct Endpoint
If you find something like:

ts
Copy
Edit
private metricsUrl = '/api/internal/metrics';
Test it manually using:

bash
Copy
Edit
curl http://localhost:3000/api/internal/metrics
or use Burp Suite Repeater to send crafted requests.

⚙️ Behind the Scenes: Technical Concepts
| Concept          | Explanation                                                                            |
| ---------------- | -------------------------------------------------------------------------------------- |
| Angular Services | Encapsulate logic for network calls using `HttpClient`.                                |
| Tree Shaking     | JS optimization that can remove unused code. But leftover endpoints might still exist. |
| Minification     | Compresses code; use DevTools `Pretty Print` to reverse.                               |
| Source Map       | Sometimes developers leave `.map` files behind – these help in de-obfuscation.         |


🎯 What to Log
File path of discovery

JS function and endpoint found

Does it require authentication? (Check headers)

Response content: metrics, status, flags?

Any security misconfigurations?

🔐 Offensive Strategy
By finding hidden metrics endpoints, you:

Bypass the need to bruteforce endpoints

Access internal telemetry

Expose sensitive operational info (uptime, internal counters, debug messages)

