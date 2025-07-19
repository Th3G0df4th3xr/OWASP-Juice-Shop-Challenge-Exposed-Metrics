📄 Method-5-Decode-Obfuscated-Links-in-JavaScript/TODO.md
markdown
Copy
Edit
# ✅ TODO - Decode Obfuscated Links in JavaScript

## 🎯 Objective:
Uncover hidden or obfuscated internal endpoints (e.g., `/metrics`, `/admin`) by decoding JavaScript files.

---

## 🔧 Step-by-Step Task Breakdown:

### [ ] Identify Target JavaScript Files
- [ ] Use browser DevTools → Network → Filter by `.js`
- [ ] Note all external script URLs or inline scripts
- [ ] Optionally use Burp Suite → Target → Filter `.js`

### [ ] Download JavaScript Files
- [ ] Use `curl`, `wget`, or Burp to export `.js` files locally

### [ ] Beautify the JavaScript
- [ ] Use online tools (e.g., https://beautifier.io/)
- [ ] Or CLI tool: `js-beautify script.js > clean.js`

### [ ] Perform Manual and Grep-Based Inspection
- [ ] Search for suspicious function calls:
  - [ ] `atob(...)` (Base64)
  - [ ] `eval(...)`
  - [ ] `String.fromCharCode(...)`
  - [ ] Concatenated strings or variables forming URLs
- [ ] Use `grep`, `strings`, or custom Python script to extract potential obfuscation

### [ ] Decode Discovered Payloads
- [ ] Decode Base64 values manually or with `base64 -d`
- [ ] Convert `String.fromCharCode` output using online tools or script
- [ ] Reconstruct URL fragments and test manually

### [ ] Validate Discovered Endpoints
- [ ] Visit decoded URL paths in browser or curl
- [ ] Look for exposed `/metrics`, `/admin`, `/internal/status` endpoints
- [ ] Observe responses and toast notifications (Juice Shop)

### [ ] Capture Screenshots and Notes
- [ ] Save decoded values
- [ ] Document source line in JavaScript file
- [ ] Capture evidence of successful access

---

## ⚠️ Notes:
- Obfuscation ≠ protection
- Decoding does not imply permission—always operate in legal and ethical test environments

---

## 🏁 Final Deliverables:
- [ ] Decoded link(s)
- [ ] Evidence of discovered endpoint
- [ ] Line references in JS file
- [ ] Screenshots of impact
