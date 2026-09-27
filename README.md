# 🛡️ Blue Team: Secure Network Defense Simulation

> A virtualized enterprise-grade security lab simulating real-world Blue Team operations — integrating SIEM, IDS/IPS, WAF, and network segmentation on VMware.

---

## 📌 Project Overview

This project designs and implements a **multi-zone network security architecture** using open-source Blue Team tools, simulating the core workflows of a Security Operations Center (SOC) environment.

**Duration:** September 2025 – December 2025  
**Role:** Security Engineer (Solo/Team Lab)  
**Environment:** VMware Workstation Pro (fully virtualized)

---

## 🏗️ Architecture

```
                        ┌─────────────────────────────────────┐
                        │           INTERNET / WAN            │
                        └──────────────┬──────────────────────┘
                                       │
                        ┌──────────────▼──────────────────────┐
                        │    pfSense Firewall / Router         │
                        │  (ACL, NAT, VLAN Routing, VPN)      │
                        └──┬─────────┬────────────┬───────────┘
                           │         │            │
              ┌────────────▼──┐  ┌───▼──────┐  ┌─▼──────────┐
              │  DMZ Zone     │  │  LAN     │  │  Guest     │
              │  (Web Server) │  │  (Users) │  │  Network   │
              │  Nginx + WAF  │  └──────────┘  └────────────┘
              └───────────────┘
                        │
              ┌──────────▼──────────────────────┐
              │     Wazuh SIEM Server (Ubuntu)   │
              │  Log collection via syslog :514  │
              │  Wazuh Manager + OpenSearch      │
              └─────────────────────────────────┘
                        ▲
              Suricata IDS/IPS (on pfSense)
              Alerts forwarded via syslog
```

---

## 🔧 Tech Stack

| Component | Tool | Role |
|-----------|------|------|
| Firewall / Router | **pfSense** | Network perimeter, ACL, VLAN, NAT |
| IDS/IPS | **Suricata** | Signature & anomaly-based detection |
| SIEM | **Wazuh** (+ OpenSearch) | Log aggregation, correlation, alerting |
| Reverse Proxy | **Nginx** | Traffic forwarding, TLS termination |
| WAF | **ModSecurity** (Nginx module) | OWASP ruleset, HTTP attack filtering |
| VPN | **Tailscale** | Secure remote management access |
| Virtualization | **VMware Workstation Pro** | Isolated multi-VM lab environment |

---

## 🌐 Network Segmentation

| Zone | Subnet | Purpose |
|------|--------|---------|
| DMZ | 192.168.10.0/24 | Public-facing web server (Nginx + WAF) |
| LAN | 192.168.20.0/24 | Internal user workstations |
| Server | 192.168.30.0/24 | Internal services, Wazuh server |
| Guest | 192.168.40.0/24 | Isolated guest network |
| Management | 192.168.50.0/24 | pfSense admin, Wazuh dashboard access |

---

## 🔑 Key Features

### 1. Centralized Log Management (SIEM)
- **Wazuh** collects logs from all zones via **syslog UDP port 514**
- pfSense version incompatibility with Wazuh Agent → solved via **agentless syslog forwarding**
- Suricata alerts auto-decoded by Wazuh's built-in Suricata decoder (EVE JSON format)
- Real-time alerting on Wazuh Dashboard (OpenSearch-powered)

### 2. IDS/IPS with Suricata
- Suricata deployed on pfSense in **IDS mode** (detection + alert, no inline block)
- Custom rules tuned to reduce false positives
- SOC L1 triage workflow: distinguish **True Positive vs False Positive** alerts

### 3. Defense-in-Depth Architecture
```
Internet → pfSense ACL → Nginx Reverse Proxy → ModSecurity WAF → Backend
```
- OWASP Core Rule Set (CRS) enabled on ModSecurity
- Nginx handles TLS termination (HTTPS → HTTP to backend)
- pfSense enforces inter-VLAN firewall rules

### 4. Incident Simulation
- Simulated attack scenarios: SYN Flood, HTTP flood, directory traversal, SQLi attempts
- Analyzed generated alerts in Wazuh, mapped to **MITRE ATT&CK** techniques
- Documented triage process for each simulated incident

---

## 📊 Results

| Metric | Value |
|--------|-------|
| Network zones | 5 (DMZ, LAN, Server, Guest, Management) |
| SIEM log sources | pfSense + Suricata + Nginx |
| Detection coverage | Signature-based (Suricata rules) + Log-based (Wazuh) |
| WAF ruleset | OWASP CRS v3.x |

---

## 📁 Repository Contents

```
├── README.md
├── report/
│   └── blueteamreport_group07.pdf    # Full technical report
├── configs/
│   ├── suricata-rules/               # Custom Suricata rules
│   ├── nginx/                        # Nginx + ModSecurity config
│   └── wazuh/                        # Wazuh decoder/rule customizations
└── screenshots/
    ├── wazuh-dashboard.png
    ├── suricata-alerts.png
    └── network-topology.png
```

---

## 🚀 Quick Setup Overview

1. Deploy pfSense VM → configure VLANs, firewall rules, Suricata package
2. Deploy Ubuntu VM → install Wazuh Manager + OpenSearch + Dashboard
3. Configure pfSense syslog to forward to Wazuh (UDP 514)
4. Deploy web server VM → configure Nginx + ModSecurity
5. Join all VMs to Tailscale for secure management access

---

## 👤 Author

**Võ Duy Hiếu** — Information Security, UIT (VNU-HCM)  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/hieuvd)
[![Kaggle](https://img.shields.io/badge/Kaggle-Profile-20BEFF)](https://www.kaggle.com/vohieu0612)
