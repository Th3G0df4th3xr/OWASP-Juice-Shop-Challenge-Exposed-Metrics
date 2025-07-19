📁 Method-5-Decode-Obfuscated-Links-in-JavaScript/README.md
markdown
Copy
Edit
# 🔍 Method 5: Decode Obfuscated Links in JavaScript

## 🎯 Challenge Context:
OWASP Juice Shop may attempt to hide sensitive endpoints—such as metrics pages—inside JavaScript files using obfuscation or encoding techniques. These URLs might not be directly visible via normal browsing but can be extracted through static code analysis.

---

## ⚔️ Goal:
Manually or programmatically identify and decode obfuscated/encoded links within JavaScript files to discover potential metrics or monitoring endpoints (e.g., `/metrics`, `/admin/status`).

---

## 🛠 Tools Required:
- Browser DevTools or Burp Suite
- JavaScript beautifiers (e.g., [beautifier.io](https://beautifier.io/))
- Base64 decoder, URI decoder
- Command-line tools: `curl`, `grep`, `js-beautify`, `strings`

---

## 🔁 Step-by-Step Attack Flow:

### 1️⃣ Locate JavaScript Files:
- Open Developer Tools → Network → JS tab
- Or use Burp Suite → Target → Find `.js` files

### 2️⃣ Download JS Files:
```bash
curl -O https://target.com/assets/main.min.js
3️⃣ Beautify JavaScript:
Use an online tool or:

bash
Copy
Edit
js-beautify main.min.js -o beautified.js
4️⃣ Search for Suspicious Strings:
Look for:

atob("...")

eval(...)

String.fromCharCode(...)

window.location=

bash
Copy
Edit
grep -iE "atob|eval|fromCharCode|location" beautified.js
5️⃣ Decode Obfuscated Content:
Base64: Use echo 'aGVsbG8=' | base64 -d

FromCharCode: Convert character codes into strings

javascript
Copy
Edit
String.fromCharCode(104, 116, 116, 112) → "http"
6️⃣ Reconstruct URLs:
Combine decoded parts. Look for endpoints like:

/metrics

/api/health

/admin/monitor

🚨 Why This Works:
Web developers may obfuscate sensitive links in production builds for obscurity or protection from automated scanners. But obfuscation ≠ security. Once decoded, these links may reveal access points to metrics or internal panels.

🧠 Pro Tips:
Some sites split URLs across variables to evade static scans—trace variable definitions!

Combine with DevTools → Debugger to trace link generation in runtime

🛡️ Remediation:
Avoid security by obscurity. Obfuscation doesn't protect sensitive endpoints.

Apply proper authentication and access control.

Use CSP and disable unnecessary endpoints in production.

📎 Example Output:
js
Copy
Edit
let link = atob("L21ldHJpY3Mvc2VjcmV0"); // → "/metrics/secret"
window.location = link;
Use this to directly access https://target.com/metrics/secret.

🏁 Outcome:
If successful, the decoded URL will lead you to the exposed metrics or admin endpoint, solving the challenge.

javascript
Copy
Edit
