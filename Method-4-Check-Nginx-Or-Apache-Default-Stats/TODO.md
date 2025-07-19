📁 Method-4-Check-Nginx-Or-Apache-Default-Stats/TODO.md
markdown
Copy
Edit
# ✅ TODO – Check Nginx or Apache Default Stats for Metrics Exposure

## 🔍 Objective:
Determine whether the target application or server exposes sensitive metrics or performance statistics via default Nginx or Apache server-status pages. These endpoints can unintentionally leak internal architecture, IPs, requests per second, and more—helping attackers perform reconnaissance.

---

## 📌 Prerequisites:
- Target running behind Nginx or Apache
- Basic enumeration tools (e.g., curl, dirsearch, gobuster, ffuf)
- Burp Suite (optional for HTTP inspection)
- Internet browser or terminal access

---

## 🚀 Step-by-Step Plan:

### 1️⃣ Identify Web Server Type:
- Run `curl -I http://<target-ip>` and observe the `Server:` response header.
- Example:
  ```bash
  curl -I http://example.com
Look for headers like Server: nginx or Server: Apache/2.4.41

2️⃣ Attempt Default Status URLs:
Try the following default URLs in the browser or via curl:

For Nginx:

http://<target>/nginx_status

http://<target>/status

For Apache:

http://<target>/server-status

Example:

bash
Copy
Edit
curl http://example.com/server-status
❗ If you get a 200 OK with actual content, it’s potentially exposing metrics like active connections, request throughput, IPs, etc.

3️⃣ Analyze Output:
For Nginx, the output may include:

Active connections

Requests per second

Reading/Writing/Waiting

For Apache, look for:

Total accesses

CPU load

Scoreboard (shows worker threads status)

Client IPs and URLs accessed

4️⃣ Use Automated Tools for Fuzzing:
Run ffuf or dirsearch to brute-force paths:

bash
Copy
Edit
ffuf -u http://<target>/FUZZ -w /usr/share/wordlists/dirb/common.txt -t 50
Observe if status, nginx_status, server-status pages exist.

5️⃣ Validate Using Burp Suite:
Open Burp Suite, forward the request to /server-status or /nginx_status.

View the HTTP response and analyze for:

Server internals

Active backend IPs

Load balancing behavior

🔬 Strategy & Rationale:
Why this method? Default server-status pages are often left exposed for admin purposes and may reveal highly sensitive operational info to unauthorized users.

What’s happening in the background? These endpoints provide real-time runtime stats. If not IP-restricted, anyone can access them.

What are we looking for?

Real-time performance metrics

Internal IPs or hostnames

Active users or requests

🧠 Remediation (If Found):
Restrict access to status endpoints using:

IP whitelisting

.htaccess rules (Apache)

allow/deny directives (Nginx)

Disable if not required.

📝 Notes:
False positives may occur if redirects or HTML responses are returned instead of actual status info.

Some systems may expose these under non-default paths (/admin/status, etc.).
