📂 Method-10-Check-for-Open-Grafana-or-Prometheus-Dashboards
🧠 Goal:
To detect and access publicly exposed Grafana or Prometheus dashboards that may be leaking sensitive application metrics, credentials, or internal system states from the OWASP Juice Shop.

🧪 Technical Methodology:
Grafana and Prometheus are widely used observability platforms. If they’re not properly secured (e.g., exposed on default ports or without authentication), attackers can exploit these dashboards to:

View real-time application metrics.

Access service names, host IPs, usernames/passwords.

Enumerate services, pods, or containers.

Chain this info into Lateral Movement or Initial Access vectors.

🔧 Tools Required:
Web browser

nmap (optional: for port scanning)

curl or wget (for CLI-based validation)

Browser DevTools (for intercepting APIs if dashboards load asynchronously)

✅ Step-by-Step Execution:
🔍 Step 1: Identify Open Ports for Grafana/Prometheus
If you have the IP of the server hosting OWASP Juice Shop, do:

bash
Copy
Edit
nmap -p 3000,9090 <target-ip>
Explanation:

3000 → default port for Grafana

9090 → default port for Prometheus

This checks if the dashboards are directly exposed.

Expected Output:

If open, ports will show open and service type (e.g., http).

🌐 Step 2: Access Dashboards via Browser
Open your browser and try:

plaintext
Copy
Edit
http://<target-ip>:3000        # Grafana
http://<target-ip>:9090        # Prometheus
Expected Behavior:

If Grafana is unauthenticated: it might show you dashboards directly or a login page with default creds.

If Prometheus is open: metrics appear directly without login, with endpoints like /metrics, /targets, /status.

🔐 Step 3: Default Credentials (Grafana only)
Try default credentials in the login page:

plaintext
Copy
Edit
Username: admin
Password: admin
Explanation:

Many deployments leave Grafana with default credentials.

If login is successful → high severity misconfiguration!

🧠 Step 4: Locate Juice Shop-specific Metrics
Inside Prometheus:

Navigate to: http://<target-ip>:9090/targets — shows all active monitoring endpoints.

Then go to: http://<target-ip>:9090/metrics — shows raw time series data.

Look for any of these metrics:

http_requests_total

app_errors_total

node_cpu_seconds_total

Custom app metrics like juiceshop_*

In Grafana:

Check if there are dashboards like:

"OWASP Juice Shop"

"Node.js Metrics"

"Docker/Container Metrics"

Click each panel to inspect data sources.

🔎 Step 5: Use DevTools to Inspect API Calls (Grafana)
Press F12 to open Developer Tools.

Go to Network tab.

Refresh Grafana dashboard.

Look for API calls to:

/api/datasources

/api/search

/api/dashboards/uid/...

These reveal the underlying structure and can leak endpoint names, backend queries, or even user tokens if misconfigured.

🧠 Why This Works:
Misconfigured monitoring tools are often exposed due to poor internal security assumptions.

Prometheus often runs without authentication by default.

Grafana is commonly misconfigured with default passwords or public dashboards.

These tools reveal internal metrics, app behavior, usernames, stack traces, etc.

It's a Reconnaissance + Information Disclosure vector.

📉 Risk Rating:
Parameter	Value
CVSS	7.5 (High)
CWE	200 (Exposure of Sensitive Information)
OWASP	A01:2021 – Broken Access Control
Impact	Application Takeover, Privilege Escalation, Lateral Movement

🛡️ Mitigation:
Never expose monitoring tools to the public Internet.

Enable authentication (OAuth/SAML/API Token).

Limit dashboards via RBAC (Role Based Access Control).

Monitor access logs to detect unauthorized access.

