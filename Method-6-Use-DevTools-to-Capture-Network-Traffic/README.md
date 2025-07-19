📁 /Method-6-Use-DevTools-to-Capture-Network-Traffic/
— focused on extremely detailed, technical, step-by-step explanation of how, why, what, and where — for total clarity.

📄 README.md
markdown
Copy
Edit
# 📡 Method 6: Use DevTools to Capture Network Traffic – OWASP Juice Shop Metrics Discovery

## 🎯 Objective:
To capture and analyze **client-side HTTP requests** made by the browser using **Developer Tools (DevTools)** in order to **enumerate internal or undocumented endpoints**, such as `/metrics`, `/rest/admin`, etc., which are **not exposed via UI** but are accessed programmatically in the background (e.g., during page load, component rendering, or asynchronous XHR calls).

This method helps attackers passively gather intelligence without brute force or fuzzing, making it stealthy and highly effective in real-world reconnaissance phases.

---

## 🧠 Technical Foundation (Why This Works)

Web applications often make asynchronous requests using **AJAX (Asynchronous JavaScript and XML)** or **Fetch API** to gather data post-page load. These calls are made to **internal APIs** or resource paths (like `/metrics`, `/rest/admin`, etc.).

Using **DevTools → Network Tab**, we can intercept, inspect, and analyze these network calls **in real-time**. This reveals backend paths, response structures, HTTP status codes, content types, and potential weaknesses (e.g., sensitive data exposure, weak authentication checks).

---

## 🛠 Prerequisites:
- Any modern browser (preferably Chrome or Firefox)
- OWASP Juice Shop instance running locally or remotely
- Basic knowledge of HTTP verbs (GET, POST, PUT, DELETE)

---

## 🚀 Step-by-Step Guide

### 🧭 Step 1: Launch OWASP Juice Shop in Browser
```bash
http://localhost:3000
Or use your hosted instance (example: http://127.0.0.1:3000)

✅ Ensure the page fully loads before continuing.

🧭 Step 2: Open Chrome DevTools
How to open:

Right-click anywhere on the page → Click Inspect

Or use shortcut: Ctrl + Shift + I (Windows/Linux) or Cmd + Option + I (Mac)

📶 Step 3: Go to Network Tab
Click on the "Network" tab in DevTools.

Then:

Ensure the Preserve Log checkbox is enabled

Select XHR, Fetch, or All filters (to focus only on programmatic requests)

📡 XHR = XMLHttpRequest, used in AJAX calls
🛰️ Fetch = newer promise-based way to make network calls

🧬 Step 4: Trigger Requests
Manual Trigger:

Navigate through various pages like "About Us", "Score Board", etc.

Click through menus, links, buttons to generate traffic

Passive Trigger:

Just wait for background JS to invoke REST calls (some pages auto-trigger metrics or config fetch)

🧠 These endpoints are often not visible in the DOM or frontend routing.

🔍 Step 5: Analyze Captured Requests
For each captured request:

Click the entry in DevTools

Go to the Headers tab:

Inspect Request URL (example: /rest/user/whoami, /rest/products/search?q=)

Review Request Method (GET/POST)

Review Status Code (e.g., 200 = success, 401 = unauthorized, 500 = error)

Inspect Content-Type (e.g., application/json, text/html)

Go to the Response tab:

Look for leaked information, stack traces, error details, metric values, etc.

🧠 Understanding the backend API structure and error messages gives a blueprint of the app logic.

📡 Step 6: Identify Hidden/Undocumented Endpoints
Focus on:

Unlinked API paths (not accessible via UI)

Admin-only or internal telemetry (e.g., /metrics, /rest/admin/application-configuration)

Large JSON payloads (look for nested keys, fields like uptime, userCount, debugMode, etc.)

💣 Step 7: Test Discovered Endpoints Manually
Using curl, Burp Suite, or browser:

bash
Copy
Edit
curl -X GET http://localhost:3000/metrics
Or use browser URL bar:

bash
Copy
Edit
http://localhost:3000/metrics
⚠️ Note if you're redirected, blocked, or receive a 401/403, this means authentication is required — this gives you clues about role-based access.

⚙️ What Happens in the Backend?
Frontend triggers a JavaScript event (like a button click or onLoad)

This executes a Fetch/XHR call to a REST API endpoint

The server processes the request (validates session/cookies, etc.)

Sends back data in JSON or HTML format

Data is consumed/rendered on the frontend

🧠 Why This Method Is Powerful:
No brute force = Stealthy

Real-time observation = Accurate path discovery

Backend error handling can reveal valuable metadata

Often reveals role-based paths (admin/user separation)

📌 Example Findings:

| URL Path                   | Method | Description                           |
| -------------------------- | ------ | ------------------------------------- |
| `/metrics`                 | GET    | Prometheus-style app metrics          |
| `/rest/admin/config`       | GET    | Admin-only configuration data         |
| `/rest/user/whoami`        | GET    | Session validation & user identity    |
| `/rest/products/search?q=` | GET    | Search endpoint (test for injections) |


🧾 Summary Checklist
 Open DevTools → Network

 Enable Preserve Log

 Filter by XHR/Fetch

 Interact with app → Observe traffic

 Analyze headers, payloads, and responses

 Identify sensitive or undocumented endpoints

 Test and document exposed paths

📷 Deliverables:
 Screenshot of DevTools showing captured requests

 Documented list of discovered paths

 Evidence of sensitive or unusual backend behavior
