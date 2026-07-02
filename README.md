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

<img width="1920" height="1080" alt="Grabbing the sysmon confid file " src="https://github.com/user-attachments/assets/c9552a4e-b464-4c5e-864b-60bf3272b32f" />

<img width="1920" height="1080" alt="Installing sysmon via powersheell and checking if running in services " src="https://github.com/user-attachments/assets/5db4af13-bc9c-4556-acf1-a2004b520d61" />

<img width="1920" height="1080" alt="Sysmon event view logs to check if installed firther correctly " src="https://github.com/user-attachments/assets/3817571e-7bec-4b82-821a-c91526ceaa06" />


### 2. Wazuh deployment
<img width="1920" height="1080" alt="Server Deployed" src="https://github.com/user-attachments/assets/263838ae-2613-4537-90db-395c776d0c39" />

<img width="934" height="458" alt="wazuh-install-summary" src="https://github.com/user-attachments/assets/7c1199a7-293f-433e-bdbb-8f194c841812" />

<img width="934" height="458" alt="wazuh-install-summary" src="https://github.com/user-attachments/assets/8fb93e0d-cecb-4298-bc57-055db91a228f" />


<img width="948" height="737" alt="Login to wazuh via VM" src="https://github.com/user-attachments/assets/536a4086-2028-482e-b47f-1b57782e495e" />

### 3. Agent and detection
<img width="1014" height="678" alt="wazuh-agent-deploy-config" src="https://github.com/user-attachments/assets/e3749f76-8925-4572-a9d1-3e757d7e2ba8" />

<img width="872" height="767" alt="wazuh-agent-active" src="https://github.com/user-attachments/assets/1272efe4-882e-48d5-8b0c-dd72f66514e9" />

<img width="1014" height="623" alt="threat-hunting-dashboard" src="https://github.com/user-attachments/assets/43caec91-ca1d-4f2b-91b5-79606726a4e2" />

<img width="1016" height="739" alt="wazuh-ssh-bruteforce-events" src="https://github.com/user-attachments/assets/0c33519f-f905-429d-b241-94a1138fb250" />


### 4. Case escalation
<img width="1909" height="1012" alt="thehive-workspace-access" src="https://github.com/user-attachments/assets/483b5e0b-b1c4-43bd-bd93-7dc21537adaa" />

<img width="1857" height="1014" alt="thehive-case-created" src="https://github.com/user-attachments/assets/af01c1a3-750d-41f4-bbd1-17bf48a93ad2" />


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
