# Wazuh Custom Rules

These files contain the custom detection logic used in the SOC Home Lab.

## Rule Summary

| Rule | Event | Purpose | Level | MITRE |
|---|---|---|---:|---|
| 100006 | 4625 | Failed Logon | 3 | — |
| 100005 | 4625 | Brute Force Correlation | 12 | T1110.001 |
| 100010 | 5156 | Nmap / Network Scan | 12 | T1046 |

## Installation

For a lab deployment, custom rules can be placed under:

```text
/var/ossec/etc/rules/
```

Wazuh recommends using the custom rules area rather than editing the built-in rules under `/var/ossec/ruleset/rules/`.

After saving rule changes, use:

```bash
/var/ossec/bin/wazuh-logtest
```

to validate rule matching. Restart the Wazuh manager to apply the rules for normal alert generation:

```bash
sudo systemctl restart wazuh-manager
```

Wazuh custom rule IDs should be in the `100000–120000` range to avoid conflicts with built-in rules.

Reference: https://documentation.wazuh.com/current/user-manual/ruleset/rules/custom.html
