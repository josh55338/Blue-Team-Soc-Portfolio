# SIEM & Detection Engineering Lab
**Wazuh + Splunk Enterprise + Sysmon | MITRE ATT&CK-Mapped Detections**

---

## Objective
This lab demonstrates the design and implementation of a complete endpoint detection pipeline. Wazuh was deployed as the primary SIEM for alerting and MITRE ATT&CK mapping, with Splunk used for centralized log ingestion, correlation, and visualization.

---

## Architecture
![Overview](Architecture/architecture-overview.png)

This environment simulates a small SOC setup with a Wazuh manager, Splunk server, and both Linux and Windows endpoints. All systems communicate over a bridged network to mimic real enterprise visibility.

**VM Specifications**  
![Wazuh VM](Architecture/wazuh-vm-specs.png) | ![Splunk VM](Architecture/splunk-vm-specs.png)  
![Linux Agent](Architecture/linux-agent-vm-specs.png) | ![Windows Agent](Architecture/windows-agent-vm-specs.png)

---

## Wazuh Setup
![Install Script](Setup-Wazuh/wazuh-install-script.png)  
![Deployment Terminal](Setup-Wazuh/wazuh-install-terminal.png)  
![Dashboard Login](Setup-Wazuh/wazuh-dashboard-login.png)  
![Empty Dashboard](Setup-Wazuh/wazuh-empty-dashboard.png)

Wazuh was installed using the official installation script and verified via terminal output and dashboard access. Initial dashboard state confirms a clean deployment prior to agent onboarding and alert generation.

---

## Splunk Integration (Universal Forwarder)
![Splunk First Login](Setup-Splunk/splunk-first-login.png)  
![Create Wazuh Index](Setup-Splunk/splunk-create-index-wazuh-alerts.png)  
![Enable Receiving Port 9997](Setup-Splunk/splunk-receiving-port-9997.png)  
![Forwarder Install on Wazuh](Setup-Splunk/splunk-forwarder-install-wazuh.png)  
![inputs.conf for alerts.json](Setup-Splunk/splunk-inputs-conf-wazuh.png)

Splunk Enterprise was configured to receive logs from Wazuh via the Universal Forwarder. Wazuh alerts were forwarded in real time from `alerts.json` into a dedicated `wazuh-alerts` index, enabling centralized analysis and dashboarding.

---

## Agent Deployment

### Linux Agent
![Deploy Command](Linux-Agent/linux-agent-deploy-command.png)  
![Connected in Wazuh](Linux-Agent/linux-agent-connected-wazuh.png)

The Linux endpoint was onboarded using the Wazuh agent deployment command. Successful connection confirmed secure communication between the agent and the Wazuh manager.

### Windows Agent + Sysmon
![Deploy Command](Windows-Agent/windows-agent-deploy-command.png)  
![Connected in Wazuh](Windows-Agent/windows-agent-connected-wazuh.png)  
![Sysmon Installed](Windows-Agent/sysmon-installed.png)

A Windows endpoint was deployed with the Wazuh agent and enhanced telemetry using Sysmon. This provided high-fidelity process, authentication, and account activity logs for detection engineering.

---

## Detected MITRE ATT&CK Techniques

### Linux Detections

#### **T1136 – Create Account**
![Command](Linux-Detections/linux-T1136-command.png)  
![Wazuh Alert](Linux-Detections/linux-T1136-wazuh.png)  
![Splunk Event](Linux-Detections/linux-T1136-splunk.png)

A new local user account was created on the Linux host. Wazuh detected the account creation event, mapped it to MITRE T1136, and forwarded the alert to Splunk with matching timestamp and context.

---

#### **T1053.003 – Scheduled Task (Cron)**
![Command](Linux-Detections/linux-T1053-command.png)  
![Wazuh Alert](Linux-Detections/linux-T1053-wazuh.png)  
![Splunk Event](Linux-Detections/linux-T1053-splunk.png)

A malicious cron job was added to simulate persistence via scheduled tasks. The activity was detected by Wazuh and accurately classified under MITRE T1053.003, then correlated in Splunk.

---

#### **T1548.003 – Privilege Escalation (Sudo Caching)**
![Command](Linux-Detections/linux-T1548-command.png)  
![Wazuh Alert](Linux-Detections/linux-T1548-wazuh.png)  
![Splunk Event](Linux-Detections/linux-T1548-splunk.png)

Privilege escalation was simulated using repeated sudo execution. Wazuh flagged the behavior as T1548.003 and forwarded the alert to Splunk, maintaining full MITRE and timestamp integrity.

---

#### **T1110.001 – Brute Force (Password Guessing)**
![Command](Linux-Detections/linux-T1110-command.png)  
![Wazuh Alert](Linux-Detections/linux-T1110-wazuh.png)  
![Splunk Event](Linux-Detections/linux-T1110-splunk.png)

Multiple failed authentication attempts were generated to simulate a brute-force attack. Wazuh detected the repeated failures and mapped the activity to MITRE T1110.001, with identical events visible in Splunk.

---

### Windows Detections (with Sysmon)

#### **T1078 – Valid Accounts**
![Command](Windows-Detections/windows-T1078-command.png)  
![Wazuh Alert](Windows-Detections/windows-T1078-wazuh.png)  
![Splunk Event](Windows-Detections/windows-T1078-splunk.png)

A legitimate account was used in a suspicious context to simulate abuse of valid credentials. The event was detected by Wazuh and mapped to T1078, then forwarded to Splunk for correlation.

---

#### **T1484 – Domain / Policy Modification**
![Command](Windows-Detections/windows-T1484-command.png)  
![Wazuh Alert](Windows-Detections/windows-T1484-wazuh.png)  
![Splunk Event](Windows-Detections/windows-T1484-splunk.png)

Security policy modification was simulated on the Windows host. Wazuh detected the change and correctly classified it as MITRE T1484, with consistent visibility in Splunk.

---

#### **T1531 – Account Manipulation**
![Command](Windows-Detections/windows-T1531-command.png)  
![Wazuh Alert](Windows-Detections/windows-T1531-wazuh.png)  
![Splunk Event](Windows-Detections/windows-T1531-splunk.png)

User account changes were performed to simulate account manipulation. The activity was detected, MITRE-mapped to T1531, and confirmed in Splunk with identical alert data.

---

## Custom Splunk Dashboard
![Dashboard Creation](Splunk-Dashboard/dashboard-create-page.png)

A custom SOC-focused dashboard was built to visualize Wazuh alerts ingested into Splunk.

**Key Panels**
![Total Alerts Query](Splunk-Dashboard/panel-total-alerts-query.png)  
Displays the total number of security alerts ingested from Wazuh.

![MITRE Techniques](Splunk-Dashboard/panel-mitre-techniques.png)  
Breaks down alerts by MITRE ATT&CK technique to highlight attacker behavior patterns.

![Agents/Hosts](Splunk-Dashboard/panel-agents-hosts.png)  
Shows alert distribution across Linux and Windows endpoints.

![Recent Events](Splunk-Dashboard/panel-recent-events-table.png)  
Provides a live SOC-style table for triage and investigation.

![Severity Distribution](Splunk-Dashboard/panel-severity-distribution.png)  
Visualizes alert severity to prioritize analyst response.

![Full Dashboard View](Splunk-Dashboard/dashboard-full-view.png)

---

## Challenges & Learnings
- Rebuilt Wazuh after API-related issues, strengthening troubleshooting skills
- Implemented real-time log forwarding using Splunk Universal Forwarder
- Achieved exact correlation across command execution, Wazuh alerts, and Splunk events
- Designed a SOC-ready dashboard using SPL for operational visibility

This lab demonstrates hands-on experience in SIEM deployment, detection engineering, endpoint telemetry, MITRE ATT&CK mapping, and SOC-level visualization.
