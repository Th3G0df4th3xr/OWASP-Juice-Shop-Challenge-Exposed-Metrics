📁 /Method-15-Check-Headers-for-Server-Exposures/TODO.md
markdown
Copy
Edit
# ✅ TODO - Check Headers for Server Exposures

## 🎯 Objective:
To identify if server headers unintentionally disclose sensitive information about backend systems, services, or software versions that may aid in further exploitation or reconnaissance.

---

## 🛠️ Tasks:

- [ ] Identify and list all target URLs or endpoints for analysis.
- [ ] Use `curl -I` and `curl -s -D-` to fetch HTTP response headers.
- [ ] Use Burp Suite to passively capture and review response headers while browsing.
- [ ] Check for the presence of revealing headers like:
  - `Server`
  - `X-Powered-By`
  - `Via`
  - `X-AspNet-Version`
  - `X-AspNetMvc-Version`
  - `X-Backend-Server`
- [ ] Use `httpx`, `nmap`, or `whatweb` to automate header extraction.
- [ ] Record any information that discloses tech stack (e.g., `nginx/1.20.1`, `PHP/7.4.3`, etc.).
- [ ] Correlate version numbers with known CVEs (Common Vulnerabilities and Exposures).
- [ ] Check for unnecessary or deprecated headers (e.g., `X-Powered-By: ASP.NET`).
- [ ] Determine if headers can be stripped, obscured, or rewritten by testing with header manipulation.
- [ ] Identify attack surface or misconfiguration risks based on header contents.

---

## ⚠️ Validation Checklist:

- [ ] Were sensitive version disclosures identified?
- [ ] Were any deprecated/legacy servers revealed (e.g., Apache 2.2)?
- [ ] Did any headers suggest misconfigurations (e.g., reverse proxy leaks)?
- [ ] Were headers consistent across endpoints, or did they indicate internal routing?

---

## 🧩 Tools Suggested:
- `curl`
- `Burp Suite`
- `httpx` (ProjectDiscovery)
- `whatweb`
- `nmap -sV`
- Custom Python scripts (`requests.get().headers`)

---

## 🧪 Final Output:
A documented list of exposed headers, associated risks, affected URLs, and suggested remediations.
