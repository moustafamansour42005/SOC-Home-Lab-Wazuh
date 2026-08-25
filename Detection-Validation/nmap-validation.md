# Nmap Detection Validation

## 1. Reload the Detection Logic

After updating the custom rule set, the Wazuh Manager is restarted:

```bash
sudo systemctl restart wazuh-manager
```

## 2. Logtest

Use:

```bash
/var/ossec/bin/wazuh-logtest
```

to simulate a Windows network connection event:

```text
Event ID = 5156
```

The validation confirms that the engine can parse important network metadata such as:

- Source Address
- Destination Port

## 3. Alert Verification

Inspect:

```text
/var/ossec/logs/alerts/alerts.json
```

and search for:

```text
rule.id == "100010"
```

## 4. Threat Hunting

The reconnaissance rule is investigated in the Wazuh Threat Hunting module.

## Reported Result

The project report records:

```text
115 distinct hits
within a 2.46-second scan window
```

This high-volume pattern is consistent with the intended Nmap reconnaissance detection scenario.
