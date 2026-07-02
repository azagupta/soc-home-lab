A self-built detection and incident response pipeline, deployed and debugged end to end.

## Overview

This lab simulates a small SOC's detect-to-respond workflow using industry-standard open source tooling: Sysmon for endpoint telemetry, Wazuh for SIEM/detection, and TheHive for case management.

## Prerequisites

- VirtualBox 7.x
- A cloud VPS with at least 4GB RAM (Vultr, DigitalOcean, etc.)
- Windows 10/11 ISO
- Basic familiarity with SSH and Linux CLI

## Architecture

- **Endpoint:** Windows 10 VM (VirtualBox) running Sysmon with Olaf Hartong's community `sysmon-modular` config, mapping events to MITRE ATT&CK techniques at the point of collection.
- **SIEM:** Wazuh 4.12 (indexer, manager, dashboard) deployed on Ubuntu 24.04, hosted on a public Vultr instance.
- **Case management:** TheHive 5.5, deployed via Docker (Cassandra + Elasticsearch + TheHive), for escalating alerts into documented incidents.

## Data flow
Windows 10 VM (Sysmon)
→ Wazuh Agent
→ Wazuh Manager (Ubuntu/Vultr)
→ Wazuh Dashboard (alerts + MITRE mapping)
→ TheHive (case escalation)

## What made this different from a typical lab

Rather than only simulating attacks internally, the Wazuh server was exposed on the public internet under real-world conditions. Within hours it had logged genuine SSH brute-force traffic from internet-wide scanning — no attacker VM needed to generate real detection data.

## Screenshots

### 1. Environment setup
<img width="1920" height="1080" alt="SHA256SUMS" src="https://github.com/user-attachments/assets/368c891e-7da8-4223-9ebe-d064ac186d92" />

![Sysmon config](screenshots/02-sysmon-config.png)
![Sysmon installed](screenshots/03-sysmon-install.png)
![Sysmon events](screenshots/04-sysmon-events.png)

### 2. Wazuh deployment
![Server deployed](screenshots/05-server-deployed.png)
![Wazuh install](screenshots/06-wazuh-install.png)
![Install summary](screenshots/07-wazuh-summary.png)
![Login page](screenshots/08-wazuh-login.png)

### 3. Agent and detection
![Agent deploy](screenshots/09-agent-deploy.png)
![Agent active](screenshots/10-agent-active.png)
![Threat hunting dashboard](screenshots/11-threat-hunting.png)
![Brute-force events](screenshots/12-bruteforce-events.png)

### 4. Case escalation
![TheHive workspace](screenshots/13-thehive-workspace.png)
![Case created](screenshots/14-case-created.png)

## Real issues hit and resolved

- `apt` lock contention from Ubuntu's daily unattended-upgrades colliding with the Wazuh installer mid-run
- Memory exhaustion running Wazuh's JVM-heavy stack alongside TheHive's Cassandra/Elasticsearch stack concurrently on a 4GB instance
- `docker-compose` v1 incompatibility with modern OCI image manifests, resolved by switching to the Compose v2 CLI plugin
- TheHive RBAC structure: distinguishing platform Superadmin (`admin` profile, no case access by design) from a working organisation's `org-admin` profile (full case/alert permissions)

## Result

A working pipeline: Sysmon telemetry → Wazuh agent → Wazuh server (alerting + MITRE mapping) → manual escalation into a TheHive case for triage.

## Next steps

- Custom Sysmon-triggered detection rules in Wazuh
- Automated Wazuh → TheHive alert forwarding (webhook integration)
- Expanding coverage with Atomic Red Team for controlled technique validation

## Tools used

Sysmon, Wazuh 4.12, TheHive 5.5, VirtualBox, Ubuntu 24.04, Docker, Vultr

## License

This project is for educational/portfolio purposes. Feel free to reference the approach, but please don't copy the write-up verbatim.

## Connect

Built by StrawHat_Aza — [LinkedIn](https://www.linkedin.com/in/aryan-gupta-260b72195/) | aryanguptas93@gmail.com
