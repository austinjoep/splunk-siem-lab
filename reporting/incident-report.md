# Incident Report: RDP Brute-Force and Unauthorized Access (Lab)

## Report Metadata

- Report ID: `IR-2026-04-SPLUNK-001`
- Analyst Role: Tier 1 SOC Analyst (Lab)
- Severity: High
- Environment: Isolated virtual lab

## Executive Summary

Splunk monitoring detected a brute-force pattern against a Windows Server RDP service. The attack originated from the Kali host and progressed from repeated failed authentication attempts to a successful remote login. Containment was executed by blocking the source IP at the Windows host firewall.

## Scope and Systems

| System | Role | IP |
|---|---|---|
| Kali Linux | Attacker + SIEM host | `10.0.2.5` |
| Windows Server 2022 | Target + log source | `10.0.2.15` |

## Detection Evidence

- High-volume failed logons: `EventCode 4625`
- Successful remote authentication: `EventCode 4624`
- Privilege assignment context: `EventCode 4672`
- Firewall block confirmations: `EventCode 5157`

Relevant query references:

- `docs/detection-queries.md`

## Timeline (Example Flow)

| Approx Time | Event |
|---|---|
| T0 | Host discovery and service enumeration completed |
| T0 + few min | Hydra brute-force begins against RDP |
| T0 + few min | Spike in `4625` detected in Splunk |
| T0 + later | Successful `4624` appears for target account |
| T0 + later | Containment rule applied in Windows Firewall |
| T0 + later | `5157` entries confirm blocked traffic |

## Root Cause (Lab Context)

- Administrative account exposed to remote logon
- Password resilience/lockout controls insufficient for brute-force resistance
- RDP exposure allowed repeated attempts from attacker source

## Containment Actions Taken

1. Blocked attacker source IP via inbound firewall rule on target
2. Monitored post-block events for continued attempts
3. Confirmed interruption of attack path

## Recommendations

1. Enforce account lockout policy and password complexity
2. Limit RDP access by source IP and network segment
3. Require MFA for privileged remote sessions
4. Alert on "failed bursts + successful login" correlation
5. Review administrative account usage and least privilege

## Final Status

- Incident state: Contained (lab)
- Additional action: Continue tuning detection thresholds and documenting repeatable SOC playbooks

## Disclaimer

This report reflects a controlled educational exercise. No unauthorized real-world activity was performed.
