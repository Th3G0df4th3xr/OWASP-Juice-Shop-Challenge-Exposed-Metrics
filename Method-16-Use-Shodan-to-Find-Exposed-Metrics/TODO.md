✅ TODO.md – Method 16 – Use Shodan to Find Exposed Metrics
markdown
Copy
Edit
# ✅ TODO – Shodan-Based Discovery of Exposed Metrics Endpoints

## [ ] Create or log into Shodan account
- [ ] Navigate to [https://www.shodan.io](https://www.shodan.io)
- [ ] Register for an API key (required for CLI usage)

## [ ] Install Shodan CLI
```bash
pip install shodan
shodan init <API_KEY>
[ ] Define relevant search filters
 Use advanced filters such as:

title:"Prometheus Time Series Collection and Processing Server"

http.favicon.hash:-1399433489

product:"Prometheus"

port:9090 or port:8080

metrics in http.html

[ ] Enumerate valid targets
 Save results locally:

bash
Copy
Edit
shodan search --fields ip_str,port,org,hostnames "metrics port:9090" > metrics_targets.txt
[ ] Analyze discovered endpoints
 Manually visit found IPs in browser (http://<IP>:9090/metrics)

 Use curl or httpie for CLI analysis:

bash
Copy
Edit
curl http://<IP>:9090/metrics
[ ] Filter out honeypots and duplicates
 Confirm real metrics content (look for keywords like go_gc_duration_seconds, process_cpu_seconds_total)

[ ] Document exposed sensitive endpoints
 Categorize based on:

Prometheus

Grafana

Node Exporter

Other custom /metrics services

[ ] Prepare exploitation or reporting strategy
 Correlate endpoint findings with CVEs or configuration leaks

 Screenshot relevant data exposure for reports

yaml
Copy
Edit
