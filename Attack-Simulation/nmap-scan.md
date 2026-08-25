# Attack Simulation — Nmap Network Reconnaissance

## Objective

Validate the custom Wazuh rule for network reconnaissance / Nmap activity.

## Lab Roles

- **Attacker:** Kali Linux
- **Target:** Windows 10
- **SIEM:** Wazuh
- **Attack Tool:** Nmap

## Detection Path

```text
Nmap
  ↓
High-volume network connections
  ↓
Windows Event ID 5156
  ↓
Wazuh Agent
  ↓
Wazuh Manager
  ↓
Rule 100010
  ↓
Level 12 Alert
```

## MITRE ATT&CK

**T1046 — Network Service Discovery**

## Validation Result

The project report records **115 distinct alerts within a 2.46-second scan window**.

## Evidence

Add screenshots to:

```text
../Screenshots/
```

Recommended evidence:

- Nmap execution in the isolated lab
- Wazuh Rule 100010 alert
- Source/Destination network fields
- Threat Hunting results
