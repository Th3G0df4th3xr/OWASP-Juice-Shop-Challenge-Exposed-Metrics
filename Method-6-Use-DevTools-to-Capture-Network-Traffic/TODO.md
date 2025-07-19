📂 /Method-6-Use-DevTools-to-Capture-Network-Traffic/:

✅ TODO - DevTools Network Traffic Analysis
🧰 Preparation
 Launch OWASP Juice Shop locally or access a hosted instance.

 Ensure browser DevTools (preferably Chrome) are accessible.

 Login as a regular user or attacker account (if needed).

 Disable browser caching from DevTools > Network tab.

🎯 Goal
 Identify hidden or undocumented metrics endpoints by inspecting all network requests made during interaction with the web application.

🔬 Execution Steps
 Open Chrome DevTools: Press F12 or Ctrl+Shift+I.

 Go to the Network tab.

 Refresh the page or perform actions like logging in, navigating pages.

 Monitor traffic, filter by:

 XHR or fetch (for API endpoints)

 Status: 200, 302, 404 for clues.

 Look for endpoints like:

/metrics

/api/metrics

/actuator

/internal

🧠 Technical Breakdown
 DevTools intercepts browser's HTTP(S) traffic, including:

Request URLs, methods, headers, response codes.

Payloads (in Preview and Response tabs).

 This helps uncover client-initiated background requests which aren’t exposed via the UI.

 Analyze how JavaScript interacts with backend services and look for hardcoded URLs or hidden API calls.

🧪 Advanced
 Export .har file for offline analysis.

 Replay suspicious endpoints using:

curl

bash
Copy
Edit
curl -X GET http://localhost:3000/metrics -H "Cookie: session=<TOKEN>"
Postman or Burp Suite.

🛡️ Post-Analysis
 Document endpoint behavior.

 Note auth requirements, headers used, response structures.

 Validate if metrics or sensitive info is leaked.
