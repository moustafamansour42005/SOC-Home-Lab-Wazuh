# Brute Force Detection Validation

## 1. Base Rule Validation

The first validation step uses:

```bash
/var/ossec/bin/wazuh-logtest
```

A simulated Windows failed-logon event with:

```text
Event ID = 4625
```

is supplied to the engine.

Expected result:

```text
Event ID 4625
    ↓
Rule 100006
    ↓
Level 3
```

## 2. Correlation Validation

The same failed-logon event is supplied repeatedly in rapid succession.

The project uses a threshold of:

```text
3 failed events
within
60 seconds
```

Expected result:

```text
Repeated Event ID 4625
        ↓
Rule 100005
        ↓
Level 12
        ↓
T1110.001 Password Guessing
```

## 3. Alert File Verification

The project checks:

```text
/var/ossec/logs/alerts/alerts.json
```

using `cat` and `jq` to verify that Rule `100005` alerts are being generated.

## 4. Threat Hunting

The Wazuh Threat Hunting module is used to correlate the attack timeline.

The reported investigation filters for:

```text
rule.id: 100005
```

and shows a dense cluster of hits around the Hydra execution timeframe.

## Conclusion

The validation demonstrates the complete detection chain from the Windows authentication event to the correlated high-severity brute-force alert.
