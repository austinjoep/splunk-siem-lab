# Containment Playbook: Block Attacker via Windows Firewall

## Trigger Condition

Initiate containment when Splunk shows sustained failed RDP authentication (`4625`) from a single source, especially if followed by a successful logon (`4624`, type `10`).

## Response Goal

Stop attacker reachability to the target host immediately while preserving logs for analysis.

## GUI Workflow (Windows Defender Firewall with Advanced Security)

1. Open **Inbound Rules**.
2. Select **New Rule...**.
3. Rule type: **Custom**.
4. Program: **All programs**.
5. Protocol/Ports: **Any** (or lock to TCP `3389` as needed).
6. Scope: add attacker source IP (`10.0.2.5` in this lab).
7. Action: **Block the connection**.
8. Profiles: apply to **Domain**, **Private**, and **Public**.
9. Name example: `IR-Block-RDP-Attacker-10.0.2.5`.

## PowerShell Alternative

```powershell
New-NetFirewallRule -DisplayName "IR-Block-RDP-Attacker-10.0.2.5" `
  -Direction Inbound `
  -RemoteAddress 10.0.2.5 `
  -Action Block
```

## Verification Checklist

- New rule appears and is enabled
- Attacker can no longer establish RDP connection
- Windows logs show blocked connection evidence (`5157`)
- Splunk dashboard reflects decrease/cessation of successful attacker attempts

## Follow-Up Hardening

- Enforce account lockout thresholds
- Restrict RDP source ranges
- Rotate privileged credentials
- Enable MFA for remote administration where possible
- Review whether RDP should remain internet- or subnet-exposed
