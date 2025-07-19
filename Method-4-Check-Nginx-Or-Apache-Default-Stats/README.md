📁 /Method-4-Check-Nginx-Or-Apache-Default-Stats/
markdown
Copy
Edit
# 🧠 Method 4: Check Nginx or Apache Default Stats Pages

## 🎯 Objective:
To identify publicly accessible **default status pages** exposed by **Nginx** or **Apache** web servers. These pages may leak **real-time metrics** like connection count, request paths, uptime, or server load — information which can be leveraged for reconnaissance or further exploitation.

---

## 🧪 Why This Method?

Most developers leave **mod_status** (Apache) or **stub_status** (Nginx) enabled for internal monitoring, but forget to restrict them via access control. Misconfigured instances expose this telemetry to anyone with network access. These endpoints become valuable for:

- **Enumerating internal endpoints**
- **Fingerprinting traffic and usage patterns**
- **Timing attacks & load inference**
- **Finding vulnerable or high-traffic paths**

---

## 🛠️ Step-by-Step Methodology (Technical & Exhaustive)

### 🔍 Step 1: Identify Web Server Technology

Use Nmap or HTTP headers to detect if the server runs Apache or Nginx.

```bash
nmap -sV -p 80,443 <target-ip>
Look for output such as:

arduino
Copy
Edit
80/tcp open  http Apache httpd 2.4.29
or:

arduino
Copy
Edit
443/tcp open  https nginx 1.14.0
Alternatively, use cURL to manually inspect HTTP headers:

bash
Copy
Edit
curl -I http://<target-ip>
Expected Output:

Server: Apache/2.4.29 (Ubuntu)

Server: nginx/1.18.0

🌐 Step 2: Try Accessing Default Status Pages
Now, test common monitoring endpoints:

For Apache mod_status:
Try:

bash
Copy
Edit
curl http://<target-ip>/server-status
or:

arduino
Copy
Edit
http://<target-ip>/server-status?auto
Expected Output:

Active connections

Idle workers

Request-per-second stats

Server version

For Nginx stub_status:
Try:

bash
Copy
Edit
curl http://<target-ip>/nginx_status
Expected Output:

yaml
Copy
Edit
Active connections: 291 
server accepts handled requests
 16630948 16630948 31070471 
Reading: 4 Writing: 292 Waiting: 5
🧠 Explanation:
These are Nginx internal metrics. Active connections, Reading, and Writing show current traffic load.

🔄 Step 3: Fuzz Alternate Paths
Use ffuf, dirsearch, or gobuster to fuzz for similar URLs:

bash
Copy
Edit
ffuf -u http://<target-ip>FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200,302
Look for hits like:

/status

/stats

/server-info

/metrics

These are often linked to mod_status or third-party monitoring plugins.

🔐 Step 4: Test for Access Controls
Sometimes these pages are IP-restricted or behind authentication. Use X-Forwarded-For or Host header spoofing to bypass:

bash
Copy
Edit
curl -H "X-Forwarded-For: 127.0.0.1" http://<target-ip>/server-status
🔎 Step 5: Analyze Output
From this point, extract useful info:

Metric	Explanation
BusyWorkers	How many worker threads are active (useful for DoS timing)
ReqPerSec	Overall throughput; identify peak hours
Scoreboard	Detailed status of Apache processes
Handled Requests	How many HTTP requests the server is processing

You can also correlate /nginx_status connections with Burp Suite Repeater timings for timing-based inference.

⚙️ Back-End Technical Insight:
mod_status and stub_status are lightweight HTTP handlers implemented within Apache and Nginx respectively.

They bypass business logic and are exposed directly by the web server, meaning no authentication or session validation by default.

If not restricted by Allow from in Apache or allow/deny in Nginx config, they are world-readable.

🧠 Strategy Summary:
We are targeting exposed telemetry meant for server admins. Since these pages offer out-of-band insights into live server operations, we treat them as attack surface:

Low risk for the attacker

High value for recon

Often overlooked by defenders

🧯 Defensive Recommendations:
If you're a blue-teamer:

Disable mod_status/stub_status if unused

Restrict access to internal IPs only

Implement HTTP auth (Basic or token-based)

Log every request made to these paths

✅ Conclusion
This method leverages the default configuration behavior of Nginx and Apache for passive data leakage. The goal is early-stage visibility without raising alarms — perfect for a stealth recon phase.
