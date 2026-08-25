# SOC Home Lab — Wazuh SIEM & Custom Threat Detection

A hands-on Security Operations Center (SOC) Home Lab developed as part of the **SOC Analyst Training at the National Telecommunication Institute (NTI)**.

The project simulates a small SOC environment in which a Kali Linux attacker performs controlled attacks against a Windows endpoint monitored by **Wazuh Agent + Sysmon**, while **Wazuh SIEM** collects telemetry, applies custom detection rules, generates alerts, and supports threat hunting.

## 🎯 Project Objectives

- Build a realistic SOC-style lab environment.
- Simulate controlled offensive activity.
- Create custom Wazuh detection rules.
- Detect Windows authentication failures and brute-force behavior.
- Detect network reconnaissance / Nmap activity.
- Map detections to MITRE ATT&CK.
- Validate detection logic with `wazuh-logtest`.
- Verify generated alerts in `alerts.json`.
- Investigate alerts through Wazuh Threat Hunting.
- Understand the workflow from attack simulation → telemetry → detection → investigation → response.

## 🏗️ Lab Architecture

| Component | Role | IP |
|---|---|---|
| Kali Linux | Attacker / Red Team | `192.168.78.132` |
| Windows 10 | Target Endpoint | `192.168.78.139` |
| Wazuh Server | SIEM / Detection & Analysis | `192.168.78.140` |

### Architecture

```text
                    ┌──────────────────────┐
                    │     Kali Linux       │
                    │       Attacker       │
                    │   192.168.78.132     │
                    └──────────┬───────────┘
                               │
                  Controlled Attacks
                    Hydra / Nmap
                               │
                               ▼
                    ┌──────────────────────┐
                    │      Windows 10      │
                    │        Target        │
                    │   192.168.78.139     │
                    │ Wazuh Agent + Sysmon │
                    └──────────┬───────────┘
                               │
                         Security Logs
                               │
                               ▼
                    ┌──────────────────────┐
                    │    Wazuh Server      │
                    │        SIEM          │
                    │   192.168.78.140     │
                    │                      │
                    │ Manager / Indexer /  │
                    │ Dashboard             │
                    └──────────────────────┘
```

## 🛡️ Wazuh Components

### Wazuh Manager
Receives endpoint telemetry, decodes events, applies detection rules, correlates events, and generates alerts.

### Wazuh Indexer
Stores and indexes parsed security data for search and analysis.

### Wazuh Dashboard
Provides visualization, alert triage, and Threat Hunting capabilities.

## 🔴 Attack Simulation

### 1. SMB Brute-Force

**Attacker:** Kali Linux  
**Target:** Windows 10  
**Attack Tool:** Hydra

The brute-force detection uses Windows **Event ID 4625**, representing a failed logon.

Detection flow:

```text
Windows Event ID 4625
        ↓
Rule 100006
        ↓
Repeated failed logons
        ↓
Rule 100005
        ↓
Level 12 Alert
        ↓
MITRE ATT&CK T1110.001
```

### 2. Network Reconnaissance / Nmap

**Attacker:** Kali Linux  
**Target:** Windows 10  
**Attack Tool:** Nmap

The custom rule looks for Windows **Event ID 5156**, representing a Windows Filtering Platform allowed connection.

Detection flow:

```text
Windows Event ID 5156
        ↓
Rule 100010
        ↓
Network reconnaissance detection
        ↓
Level 12 Alert
        ↓
MITRE ATT&CK T1046
```

The validation reported **115 distinct hits within a 2.46-second scan window**.

## 🔐 Custom Detection Rules

| Rule ID | Purpose | Event ID | Level | MITRE ATT&CK |
|---|---|---:|---:|---|
| `100006` | Failed Logon Base Rule | `4625` | 3 | — |
| `100005` | Brute-Force Correlation | `4625` | 12 | `T1110.001` |
| `100010` | Nmap / Network Scan Detection | `5156` | 12 | `T1046` |

### Rule 100006 — Failed Logon

This is the base detection rule. It identifies Windows Security Event ID `4625`.

It is intentionally low severity because a single failed login does not necessarily mean a brute-force attack.

### Rule 100005 — Brute Force

This rule correlates repeated failed-logon events. The project validation uses a threshold of **3 failed events within 60 seconds**, associated with the same source context.

The resulting alert is Level 12 and mapped to **MITRE ATT&CK T1110.001 — Password Guessing**.

### Rule 100010 — Nmap Detection

This rule detects Event ID `5156` under the Wazuh Windows network-event baseline and flags the high-volume network activity associated with the Nmap scan.

It is mapped to **MITRE ATT&CK T1046 — Network Service Discovery**.

## 🔎 Alert Investigation

The generated Wazuh alerts provide useful SOC investigation fields, including:

- Source IP
- Source Port
- Destination IP
- Destination Port
- Windows Event ID
- Wazuh Rule ID
- Alert Level / Severity
- MITRE ATT&CK technique
- Timestamp
- Attack timeline

This information allows a SOC analyst to move from alert generation to investigation and threat hunting.

## 🧪 Detection Validation

### Brute-Force Validation

The project validated the detection in multiple stages:

```text
wazuh-logtest
      ↓
Event ID 4625
      ↓
Rule 100006
      ↓
Repeated events
      ↓
Rule 100005
      ↓
Level 12
      ↓
MITRE T1110.001
      ↓
alerts.json
      ↓
Threat Hunting
```

The validation confirmed that the base rule detects the failed-logon event and that repeated events trigger the composite brute-force rule.

The project also inspected:

```text
/var/ossec/logs/alerts/alerts.json
```

using `cat` and `jq` to verify alert generation.

### Nmap Validation

The Wazuh manager was restarted after the custom rule configuration, then `wazuh-logtest` was used to simulate Event ID `5156`.

The test successfully parsed important metadata including the source address and destination port.

The resulting alerts were then checked in:

```text
/var/ossec/logs/alerts/alerts.json
```

and investigated through Threat Hunting.

## 🔍 Threat Hunting

Threat Hunting was used to investigate the timeline and correlate alerts.

For the brute-force validation, the dashboard was filtered using:

```text
rule.id: 100005
```

This revealed a dense cluster of alerts corresponding to the Hydra execution timeframe.

For the Nmap validation, the reconnaissance rule was investigated and the project recorded 115 hits in the short scan window.

## 🚨 Response Concept

The detection rules can be integrated with Wazuh Active Response to automate mitigation after a confirmed high-severity alert.

For example:

```text
Rule 100005 / 100010
        ↓
High-Severity Alert
        ↓
Active Response
        ↓
Temporary Source-IP Block
        ↓
SOC Investigation
```

**Important:** the supplied project report documents detection and validation. It does not document that Active Response was actually executed during the reported tests. Therefore, Active Response should be described as a response design/extension unless it is separately implemented and documented.

## 📂 Repository Structure

```text
SOC-Home-Lab-Wazuh/
├── README.md
├── Architecture/
│   └── README.md
├── Wazuh-Rules/
│   ├── brute-force-rules.xml
│   ├── nmap-detection-rule.xml
│   └── README.md
├── Attack-Simulation/
│   ├── brute-force.md
│   └── nmap-scan.md
├── Detection-Validation/
│   ├── brute-force-validation.md
│   └── nmap-validation.md
├── Threat-Hunting/
│   └── threat-hunting.md
├── Response/
│   └── active-response-design.md
├── Screenshots/
│   └── .gitkeep
├── Reports/
│   └── README.md
├── .gitignore
└── LICENSE
```

## 🧰 Technologies

- Wazuh SIEM
- Wazuh Agent
- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Sysmon
- Kali Linux
- Windows 10
- Hydra
- Nmap
- MITRE ATT&CK
- `wazuh-logtest`
- `jq`

## 🎓 Training

**SOC Analyst Training — National Telecommunication Institute (NTI)**

This project was developed as a practical application of SOC monitoring, detection engineering, log analysis, MITRE ATT&CK mapping, alert triage, and threat hunting concepts.

## ⚠️ Lab / Ethics

All attacks in this repository are intended for an isolated, authorized lab environment only. Do not use the techniques against systems you do not own or have explicit permission to test.

## 📄 Project Report

The original project report is referenced in `Reports/README.md`. Add the supplied PDF there before publishing the repository if you want the full report included.
