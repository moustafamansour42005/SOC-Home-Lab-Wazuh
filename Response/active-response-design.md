# Active Response Design

## Purpose

The project can be extended from detection and investigation into automated response.

The recommended lab response is a temporary source-IP block after a confirmed high-severity detection.

## Proposed Flow

```text
Rule 100005 / Rule 100010
        ↓
High-Severity Alert
        ↓
Wazuh Active Response
        ↓
Temporary Source-IP Block
        ↓
SOC Analyst Investigation
```

## Brute Force

Trigger:

```text
Rule 100005
```

Proposed action:

```text
Temporarily block the attacker's source IP.
```

## Nmap

Trigger:

```text
Rule 100010
```

Proposed action:

```text
Temporarily block the scanner's source IP.
```

## Important Project Note

The supplied project report documents the detection and validation workflow. It does **not** document an executed Active Response test. Therefore this directory describes a response design/extension, not a claim that the response was executed during the reported experiment.

When implementing Active Response, validate the exact command and endpoint configuration for the installed Wazuh version and Windows agent.

Wazuh documentation:
https://documentation.wazuh.com/current/user-manual/capabilities/active-response/how-to-configure.html
