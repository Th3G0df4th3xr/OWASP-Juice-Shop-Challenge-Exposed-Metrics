📁 Method-17-Analyze-SourceMap-Files-for-Metrics-Clues/TODO.md
markdown
Copy
Edit
# ✅ TODO - Analyze SourceMap Files for Metrics Clues

## 🎯 Objective
Systematically uncover sensitive internal API paths, variable references, or logic exposure from `.map` files bundled with front-end JavaScript frameworks that may reveal paths to metrics endpoints.

---

## 🔍 Preparation

- [ ] Identify the main `.js` bundle used in the web app.
- [ ] Check if a corresponding `.map` file is publicly accessible (usually by appending `.map` to the JS file).
- [ ] Install source-map-explorer or similar tools for static analysis.

---

## 🔨 Action Steps

- [ ] Use browser DevTools (Network tab) to locate large JS bundles (e.g., `main.js`, `vendor.js`, etc.).
- [ ] Try to access the SourceMap (e.g., `https://target.com/static/js/main.js.map`).
- [ ] If available, download it for offline inspection.

```bash
wget https://target.com/static/js/main.js.map -O main.js.map
 Analyze the source map using:

bash
Copy
Edit
npm install -g source-map-explorer
source-map-explorer main.js main.js.map
 Inspect the de-obfuscated code for:

Hardcoded API paths

Exposed endpoints

Metric-related functions or comments

Hidden environment variables

🧠 Analysis & Exploitation
 Correlate any internal metric endpoints found (e.g., /metrics, /internal/stats, etc.) with what’s exposed.

 Test those endpoints using:

bash
Copy
Edit
curl -i https://target.com/internal/metrics
 Try path brute-forcing if references suggest hidden endpoints:

bash
Copy
Edit
ffuf -u https://target.com/FUZZ -w endpoints.txt -t 100
🧼 Post-Enumeration
 Document all findings.

 Tag exposed metric endpoints and their sensitivity.

 Suggest removing public access to .map files or using build obfuscation.

📌 Notes
 This method is passive, avoid active fuzzing on production systems without approval.

 Combine with browser-based reverse engineering if needed.

 Consider automating the scan of .map files in future audits.
