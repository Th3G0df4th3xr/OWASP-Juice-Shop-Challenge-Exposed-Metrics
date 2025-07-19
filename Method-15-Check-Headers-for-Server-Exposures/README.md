📂 /Method-15-Check-Headers-for-Server-Exposures/
markdown
Copy
Edit
# 🧠 Method 15: Check Headers for Server Exposures

## 📌 Objective

Identify information disclosure vulnerabilities in **HTTP response headers**, which may reveal server types, versions, or technologies that help an attacker craft tailored exploits. This passive reconnaissance technique is foundational in offensive security to **fingerprint the target stack** before moving to active probing or exploitation.

---

## 🛠️ Tools Required

- `curl` (Command-line HTTP client)
- `httpx` (For mass scanning and header enumeration)
- `Burp Suite` (For deep manual header inspection)
- `Nmap` (For service version detection)
- `Wappalyzer` browser plugin (Optional GUI-based tech stack enumeration)

---

## 📖 Step-by-Step Breakdown

### 🔍 Step 1: Basic Header Enumeration via curl

```bash
curl -I https://targetsite.com
-I flag sends a HEAD request (returns headers only).

✅ Expected Output: You may see headers like:

makefile
Copy
Edit
Server: Apache/2.4.18 (Ubuntu)
X-Powered-By: PHP/7.2.10
📌 Explanation:
These headers expose web server version (Apache/2.4.18) and backend language (PHP/7.2.10). This information is critical for vulnerability mapping (e.g., against known CVEs).

🚀 Step 2: Use httpx for Scanning at Scale
bash
Copy
Edit
cat urls.txt | httpx -title -server -tech-detect -status-code -silent
urls.txt should contain target URLs.

-server: Extracts the Server: header.

-tech-detect: Uses fingerprinting to identify technologies.

✅ Output will show a list of sites with server details and exposed stack metadata.

🧠 Why:
Useful for bug bounty or external pentesting when handling multiple domains. Fingerprinting helps prioritize targets based on exploitable versions.

💻 Step 3: Deep Manual Inspection via Burp Suite
Intercept a request in Burp.

Right-click → "Send to Repeater"

Analyze Response tab.

Focus on:

Server

X-Powered-By

X-AspNet-Version

X-AspNetMvc-Version

X-Backend-Server

Via, X-CDN, X-Akamai-*

🧠 Back-End Detail:
Each of these headers reveals internal implementations, which may assist in:

Targeting old tech stacks

Identifying misconfigured reverse proxies

Finding unprotected origin servers (e.g., via CDN leaks)

🔬 Step 4: Header Diffing Using Nmap
bash
Copy
Edit
nmap -sV --script http-headers.nse -p 80,443 targetsite.com
-sV: Version detection

--script http-headers.nse: Runs the script to pull HTTP headers

✅ This reveals differences between HTTP vs HTTPS headers, possible redirect leakage, or server discrepancies.

📌 Why This Works
Headers are the first layer of metadata exposed by web servers. Misconfigured headers can lead to:

Unintended version disclosure

Misidentified technologies (e.g., Node.js via X-Powered-By)

CDN exposure via Via, X-Cache

Possible access to development or staging environments

🔐 Offensive Use Case
If a header shows:

arduino
Copy
Edit
Server: Apache/2.4.18 (Ubuntu)
You can search for:

bash
Copy
Edit
searchsploit Apache 2.4.18
…to check for local/remote exploits.

Example:

CVE-2017-15715 — Path Traversal

CVE-2017-9788 — Optionsbleed

🎯 Strategy & Offensive Mindset
This method follows OSINT-first principle—maximum info, minimum interaction. It is ideal for low-noise reconnaissance and helps:

Decide the attack surface (tech stack, known exploits)

Build a vulnerability hypothesis

Customize payloads (e.g., PHP-specific LFI attacks)

✅ Conclusion
Checking headers is not just passive—it’s the foundation of attack strategy formulation. Every disclosure reduces the time between recon and exploitation. This methodology also respects stealth, allowing pentesters or red teamers to avoid triggering WAFs or EDRs too early in the chain.

