# Lab Architecture

## Nodes

### Kali Linux — Attacker

```text
192.168.78.132
```

Used to generate controlled offensive activity.

### Windows 10 — Target

```text
192.168.78.139
```

Monitored with Wazuh Agent and Sysmon.

### Wazuh Server — SIEM

```text
192.168.78.140
```

Runs the Wazuh Manager, Indexer, and Dashboard in the all-in-one lab architecture.

## Data Flow

```text
Attacker
   ↓
Target Endpoint
   ↓
Wazuh Agent
   ↓
Wazuh Manager
   ↓
Rules / Correlation
   ↓
Wazuh Indexer
   ↓
Wazuh Dashboard
   ↓
SOC Analyst
```
