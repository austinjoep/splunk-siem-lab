# Post-Bruteforce Validation: xfreerdp Access

## Objective

Validate whether recovered credentials can establish an interactive RDP session and generate the expected success telemetry in Splunk.

## Install Client

```bash
sudo apt update
sudo apt install freerdp3-x11 -y
```

## Login Command

```bash
xfreerdp /u:Administrator /p:'<lab-password>' /v:10.0.2.15
```

## Expected Outcome

- Windows desktop session opens from Kali
- Splunk records successful logon events:
  - `4624` (successful authentication)
  - `4672` (special privileges assigned, where applicable)
- RDP session context appears with logon type `10` (RemoteInteractive)

## Common Failure Causes

- RDP not enabled on target
- Target firewall blocking port `3389`
- Incorrect credentials
- Account lockout policy triggered by brute-force stage

## Analyst Interpretation

A successful RDP logon immediately after repeated `4625` failures is a strong compromise indicator and should trigger containment workflow.
