# Network Monitoring & Intrusion Detection Lab (Zeek + Suricata)

**Lab #2 – Blue Team / SOC Foundations**

## Overview
This lab demonstrates the deployment of a dedicated network monitoring sensor using **Zeek** for network telemetry and **Suricata** for signature-based intrusion detection. A separate **Kali Linux attacker VM** was used to generate controlled malicious and suspicious traffic. All activity was captured, validated, and correlated using concrete logs and alerts to reflect real-world SOC investigation workflows.

The focus of this lab is **visibility, detection, validation, and correlation** — not dashboards.

---

## Tools & Technologies
- **Zeek** – Network Security Monitoring (metadata & telemetry)
- **Suricata** – Intrusion Detection System (IDS)
- **Emerging Threats Open** – Community IDS ruleset
- **Kali Linux** – Attack simulation
- **Ubuntu Linux** – Monitoring sensor
- **VMware Workstation**
- **Host-Only Networking (vmnet1)**

---

## Lab Architecture

### Virtual Machines
- **Monitoring VM (Ubuntu Linux)**
  - Zeek
  - Suricata
  - Python HTTP server (for controlled traffic simulation)

- **Attacker VM (Kali Linux)**
  - ICMP, Nmap, HTTP beaconing, and malware delivery simulation

### Network Design
- Host-Only network to eliminate external noise
- NAT enabled only temporarily for package installation
- East–west traffic visibility
- Predictable IP addressing for clean correlation

![Lab Architecture](screenshots/01-architecture/01-lab-architecture.png)

---

## Key Challenges & Troubleshooting

### Internet vs Monitoring Accuracy
- Host-Only blocked internet access required for installs and updates
- Solution:
  - Temporarily enabled NAT
  - Completed installs and `suricata-update`
  - Disabled NAT and returned to Host-Only

### IP Address Changes
- NAT ↔ Host-Only switching caused IP changes
- Broke early correlation
- Fixed by:
  - Verifying IPs on both VMs
  - Regenerating traffic after stabilization

---

## Environment Setup & Networking

- Kali attacker VM created  
- Ubuntu monitoring VM created  
- Host-Only network configured and verified  

![Kali VM Created](screenshots/02-vm-setup/02-kali-vm-created.png)  
![Monitoring VM Created](screenshots/02-vm-setup/03-monitoring-vm-created.png)  
![Host-Only Network Configuration](screenshots/02-vm-setup/04-host-only-network.png)

---

## Zeek Deployment & Validation

- Zeek installed on the Monitoring VM
- Live capture enabled on the monitoring interface
- Focused on:
  - `conn.log`
  - `http.log`
  - `files.log`

![Zeek Installed](screenshots/03-zeek-setup/01-zeek-installed.png)  
![Zeek Interface Configuration](screenshots/03-zeek-setup/02-zeek-interface.png)

---

## ICMP Visibility Test (Telemetry vs Alerting)

### Attacker Action
- ICMP echo requests sent from Kali

![Kali: Ping Command Executed](screenshots/05-icmp-test/01-Kali-ping-command.png)

### Defender Evidence (Zeek)
- ICMP sessions recorded in `conn.log`
- No alerts generated (expected behavior)

![Zeek: ICMP Entries in conn.log](screenshots/05-icmp-test/02-zeek-icmp-conn-log.png)

This demonstrates that **visibility does not equal alerting**.

---

## Suricata Deployment & Configuration

- Suricata installed and configured in IDS mode
- Interface aligned with Zeek
- Alerts written to `eve.json`

![Suricata Installed](screenshots/04-suricata-setup/01-suricata-installed.png)  
![Suricata Interface Configuration](screenshots/04-suricata-setup/02-suricata-config-interface.png)  
![Suricata Running](screenshots/04-suricata-setup/03-suricata-running.png)

---

## Custom ICMP Rule Development

- Custom Suricata ICMP rule created
- Rule loaded and verified

![Custom ICMP Rule Created](screenshots/06-custom-rule-test/01-custom-icmp-rule.png)  
![Suricata Rules Loaded Confirmation](screenshots/06-custom-rule-test/02-suricata-rule-loaded.png)

### Validation
- ICMP traffic regenerated from Kali
- Suricata alert triggered

![Suricata: Custom ICMP Alert](screenshots/06-custom-rule-test/03-suricata-icmp-alert.png)

---

## Emerging Threats Open (ET Open) Integration

- Suricata updated using `suricata-update`
- ~63,000 rules downloaded
- ~47,000 rules enabled
- Clean restart completed

![Suricata-Update Command & Output](screenshots/07-et-rules/01-suricata-update.png)  
![ET Rules Loaded Confirmation](screenshots/07-et-rules/02-et-rules-loaded.png)

---

## Reconnaissance Detection – Nmap

### Attacker Action
- Full TCP SYN scan executed from Kali

![Kali: Nmap Command Executed](screenshots/08-nmap-scan/01-kali-nmap-command.png)

### Defender Evidence
- Multiple ET Open scan signatures triggered
- Alerts logged in `eve.json`

![Suricata: Nmap Scan Alerts](screenshots/08-nmap-scan/02-suricata-nmap-alerts.png)

This confirms correct packet capture, rule loading, and IDS functionality.

---

## HTTP Beaconing Simulation (C2-Like Traffic)

### Simulation Setup & Attacker Action
- Python HTTP server hosted on Monitoring VM
- Repeated `curl` requests from Kali with regular timing, randomized parameters, and consistent User-Agent

![Kali: Beacon Script/Command Running](screenshots/09-http-beaconing/01-kali-beacon-command.png)

### Defender Evidence
- Zeek `http.log` revealed clear periodic request patterns
- Behavioral detection via pattern analysis (no signature alert needed)

![Zeek: HTTP Beacon Pattern in http.log](screenshots/09-http-beaconing/02-zeek-http-beacon-pattern.png)

---

## Malware Delivery Detection – EICAR

### Attacker Action
- EICAR test file downloaded from Monitoring VM via `curl`

![Kali: EICAR Download Command](screenshots/10-eicar-test/01-kali-eicar-download.png)

### Defender Evidence
- Suricata triggered `MALWARE EICAR Test File` alert
- Zeek logged file transfer metadata

![Suricata: EICAR Malware Alert](screenshots/10-eicar-test/02-suricata-eicar-alert.png)  
![Zeek: File Transfer Logs](screenshots/10-eicar-test/03-zeek-file-log.png)

---

## Cross-Tool Correlation (Same Event in Different Tools)

The same EICAR malware delivery event captured from multiple perspectives:

- **Top screenshot:** Suricata `eve.json` alert showing the MALWARE detection with full HTTP details and file info
- **Bottom screenshot:** Zeek `http.log` showing the corresponding HTTP session and file transfer metadata

This side-by-side view demonstrates how SOC analysts validate a signature-based alert (Suricata) using independent network telemetry (Zeek) — matching timestamps, IPs, ports, User-Agent, and file name.

![Same EICAR Event: Suricata Alert (top) + Zeek Metadata (bottom)](screenshots/11-correlation/01-zeek-suricata-correlation.png)

Correlation confirmed using **independent telemetry and IDS alerts**, reflecting real SOC validation workflows.

---

## Key SOC Skills Demonstrated

- Network sensor placement and isolation
- Telemetry vs alerting analysis
- Custom IDS rule creation
- Community ruleset integration
- Reconnaissance detection
- Behavioral beacon analysis
- Malware delivery detection
- Multi-tool correlation and validation
- Real-world troubleshooting

---

## Lessons Learned
- No alert ≠ no visibility
- Infrastructure misalignment breaks detection
- Signature detection and telemetry complement each other
- Correlation is a validation step, not an assumption

---

## Future Improvements
- Forward logs to a SIEM
- Enable Zeek Intel framework
- Simulate advanced C2 frameworks
- Tune ET Open rules and reduce false positives

---

**All screenshots are organized in numbered subfolders inside `/screenshots/` (e.g., `01-architecture/`, `02-vm-setup/`, etc.) with exact filenames as referenced above.**
