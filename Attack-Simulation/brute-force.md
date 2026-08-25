# Attack Simulation — SMB Brute Force

## Objective

Validate the custom Wazuh brute-force detection pipeline using a controlled SMB brute-force scenario.

## Lab Roles

- **Attacker:** Kali Linux
- **Target:** Windows 10
- **SIEM:** Wazuh
- **Attack Tool:** Hydra

## Detection Path

```text
Hydra
  ↓
Windows authentication failures
  ↓
Event ID 4625
  ↓
Wazuh Agent
  ↓
Wazuh Manager
  ↓
Rule 100006
  ↓
Correlation threshold
  ↓
Rule 100005
  ↓
Level 12 Alert
```

## MITRE ATT&CK

**T1110.001 — Password Guessing**

## Evidence

Add screenshots to:

```text
../Screenshots/
```

Recommended evidence:

- Hydra execution in the isolated lab
- Windows failed-logon events
- Wazuh alert for Rule 100005
- Threat Hunting timeline
