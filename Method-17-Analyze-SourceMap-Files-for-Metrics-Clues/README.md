📂 Method-17-Analyze-SourceMap-Files-for-Metrics-Clues/
📌 Objective:
Leverage exposed JavaScript source map files (.map) to deobfuscate production JS, extract hidden API routes, debug logic, and uncover potential references to metrics endpoints such as /metrics, /prometheus, or internal-only telemetry services.

🧠 Why This Method Works (Technical Background)
Modern front-end frameworks (React, Angular, Vue, etc.) bundle and minify JavaScript code for production. To aid debugging, developers sometimes leave .map files on the server. These are source maps—a JSON-based reverse map that links minified code back to the original source code.

If exposed, these .map files can:

Help attackers reconstruct the entire front-end logic.

Leak function names, internal paths, stack traces, and telemetry calls.

Reveal hidden API endpoints, potentially leading to metrics dashboards, SSRF, or backend enumeration.

⚙️ Step-by-Step Instructions
🧭 Step 1: Identify SourceMap Exposure
🔍 Use Burp Suite or browser DevTools to locate .map files
Open Developer Tools (F12 in browser)

Go to the Network tab

Reload the page and look for any files ending in .js.map or .map

Alternatively, use curl or dirsearch:

bash
Copy
Edit
curl -I https://target.site/static/js/main.js.map
If you get 200 OK, the file exists.

❗ Exposed .map files are not meant to be in production. This is a misconfiguration.

🧰 Step 2: Download the .map File
Use wget or curl:

bash
Copy
Edit
wget https://target.site/static/js/main.js.map -O source.map
🧪 Step 3: Deobfuscate the JavaScript using Source Map
🛠 Tool: source-map-explorer
This tool maps .map files to their original readable JavaScript.

🔹 Install source-map-explorer:
bash
Copy
Edit
npm install -g source-map-explorer
🔹 Then use it:
bash
Copy
Edit
source-map-explorer main.js source.map
This will generate a browser view showing all functions, file paths, and structure.

✅ Expected Output:
Human-readable file names

Original variable/function names

Potential API endpoints or internal telemetry service routes

🔦 Step 4: Analyze Decompiled Output
Search for keywords like:

metrics

prometheus

grafana

internal

/api/debug

fetch( or axios.get(

🔍 You can grep locally:
bash
Copy
Edit
grep -i "metrics" main.js
grep -Ei "fetch|axios|get\(" main.js
Look for suspicious endpoints that might indicate metrics collection, telemetry, or internal-only APIs.

💡 Step 5: Confirm the Discovered Metrics Endpoint
Use curl or httpie to probe the endpoints:

bash
Copy
Edit
curl -i https://target.site/api/metrics
Check for:

Prometheus-formatted output (# HELP, # TYPE, etc.)

JSON response with internal stats

Unauthenticated access!

📉 If found, this is insecure metrics exposure—a critical misconfiguration.

🧠 What’s Happening Behind the Scenes?
Action	What it does
.map discovery	Finds source maps that can be used to reverse engineer obfuscated JS.
Downloading .map	Retrieves a JSON-based mapping from minified code to original source
Source Map Explorer	Parses and visualizes minified JS + sourcemap to reveal actual logic
Keyword Analysis	Looks for hidden internal calls or telemetry collection APIs
Endpoint Testing	Probes discovered endpoints to confirm if sensitive metrics are accessible

🛡️ Why It Matters (Strategic Rationale)
Metrics endpoints often expose internal server stats, environment details, or even secrets.

Attackers can pivot to SSRF, leak system-level data, or map infrastructure.

This approach takes advantage of developer negligence in CI/CD pipelines.

🧠 Glossary of Terms
Term	Meaning
.map file	A source map that connects minified JavaScript back to original source code
Minification	Process of shortening JS code for performance (e.g., renaming function loadData() to a())
source-map-explorer	CLI tool to decode .map files into structured, readable code
Telemetry	Internal instrumentation used by apps to track performance and health
Prometheus	A popular open-source monitoring tool that exposes /metrics endpoints
SSRF	Server-Side Request Forgery – attacker tricks server into accessing internal resources

🚩 Red Flags to Watch
metrics endpoint without authentication

Source map reveals sensitive logic

Use of fetch("/internal/metrics") in client-side code

Embedded credentials or tokens inside source maps

