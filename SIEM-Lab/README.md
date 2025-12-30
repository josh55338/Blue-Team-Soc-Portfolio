# SIEM & Detection Engineering Lab
**Wazuh + Splunk Enterprise + Sysmon | MITRE ATT&CK-Mapped Detections**

### Objective
Built a complete endpoint detection pipeline with centralized alerting, real-time log forwarding, and advanced visualization in Splunk.

### Architecture
![Overview](Architecture/architecture-overview.png)

VM Specifications:  
![Wazuh VM](Architecture/wazuh-vm-specs.png) | ![Splunk VM](Architecture/splunk-vm-specs.png)  
![Linux Agent](Architecture/linux-agent-vm-specs.png) | ![Windows Agent](Architecture/windows-agent-vm-specs.png)

### Wazuh Setup
![Install Script](Setup-Wazuh/wazuh-install-script.png)  
![Deployment Terminal](Setup-Wazuh/wazuh-install-terminal.png)  
![Dashboard Login](Setup-Wazuh/wazuh-dashboard-login.png)  
![Empty Dashboard](Setup-Wazuh/wazuh-empty-dashboard.png)

### Splunk Integration (Universal Forwarder)
![Splunk First Login](Setup-Splunk/splunk-first-login.png)  
![Create Wazuh Index](Setup-Splunk/splunk-create-index-wazuh-alerts.png)  
![Enable Receiving Port 9997](Setup-Splunk/splunk-receiving-port-9997.png)  
![Forwarder Install on Wazuh](Setup-Splunk/splunk-forwarder-install-wazuh.png)  
![inputs.conf for alerts.json](Setup-Splunk/splunk-inputs-conf-wazuh.png)

### Agent Deployment

**Linux Agent**  
![Deploy Command](Linux-Agent/linux-agent-deploy-command.png)  
![Connected in Wazuh](Linux-Agent/linux-agent-connected-wazuh.png)

**Windows Agent + Sysmon**  
![Deploy Command](Windows-Agent/windows-agent-deploy-command.png)  
![Connected in Wazuh](Windows-Agent/windows-agent-connected-wazuh.png)  
![Sysmon Installed](Windows-Agent/sysmon-installed.png)

### Detected MITRE ATT&CK Techniques

#### Linux Detections

- **T1136 - Create Account**  
  Simulated local account creation on the Linux agent using `useradd` to demonstrate persistence technique.  
  ![Command](Linux-Detections/linux-T1136-command.png)  
  ![Wazuh Alert](Linux-Detections/linux-T1136-wazuh.png)  
  ![Splunk Event](Linux-Detections/linux-T1136-splunk.png)

- **T1053.003 - Scheduled Task (Cron)**  
  Created a persistent cron job to mimic scheduled task abuse for persistence.  
  ![Command](Linux-Detections/linux-T1053-command.png)  
  ![Wazuh Alert](Linux-Detections/linux-T1053-wazuh.png)  
  ![Splunk Event](Linux-Detections/linux-T1053-splunk.png)

- **T1548.003 - Privilege Escalation (Sudo Caching)**  
  Exploited sudo caching mechanism to elevate privileges without re-authentication.  
  ![Command](Linux-Detections/linux-T1548-command.png)  
  ![Wazuh Alert](Linux-Detections/linux-T1548-wazuh.png)  
  ![Splunk Event](Linux-Detections/linux-T1548-splunk.png)

- **T1110.001 - Brute Force (Password Guessing)**  
  Performed multiple failed login attempts to trigger brute force detection.  
  ![Command](Linux-Detections/linux-T1110-command.png)  
  ![Wazuh Alert](Linux-Detections/linux-T1110-wazuh.png)  
  ![Splunk Event](Linux-Detections/linux-T1110-splunk.png)

#### Windows Detections (with Sysmon)

- **T1078 - Valid Accounts**  
  Simulated abuse of valid credentials for lateral movement/initial access.  
  ![Command](Windows-Detections/windows-T1078-command.png)  
  ![Wazuh Alert](Windows-Detections/windows-T1078-wazuh.png)  
  ![Splunk Event](Windows-Detections/windows-T1078-splunk.png)

- **T1484 - Domain Policy Modification**  
  Modified group policy to demonstrate domain trust abuse (Windows-specific).  
  ![Command](Windows-Detections/windows-T1484-command.png)  
  ![Wazuh Alert](Windows-Detections/windows-T1484-wazuh.png)  
  ![Splunk Event](Windows-Detections/windows-T1484-splunk.png)

- **T1531 - Account Manipulation**  
  Altered account policies/password settings to show manipulation technique.  
  ![Command](Windows-Detections/windows-T1531-command.png)  
  ![Wazuh Alert](Windows-Detections/windows-T1531-wazuh.png)  
  ![Splunk Event](Windows-Detections/windows-T1531-splunk.png)

### Custom Splunk Dashboard
![Dashboard Creation](Splunk-Dashboard/dashboard-create-page.png)

Key Panels:  
![Total Alerts Query](Splunk-Dashboard/panel-total-alerts-query.png)  
![MITRE Techniques](Splunk-Dashboard/panel-mitre-techniques.png)  
![Agents/Hosts](Splunk-Dashboard/panel-agents-hosts.png)  
![Recent Events](Splunk-Dashboard/panel-recent-events-table.png)  
![Severity Distribution](Splunk-Dashboard/panel-severity-distribution.png)

![Full Dashboard View](Splunk-Dashboard/dashboard-full-view.png)

### Challenges & Learnings
- Rebuilt Wazuh VM after API issues — gained troubleshooting resilience
- Configured real-time log forwarding using Splunk Universal Forwarder
- Achieved exact field, timestamp, and MITRE ID correlation between Wazuh and Splunk
- Developed custom dashboard with SPL for alert aggregation and visualization

This lab demonstrates practical skills in SIEM deployment, endpoint monitoring, detection engineering, log integration, and threat visualization — ready for real-world SOC Tier 1/2 tasks.
