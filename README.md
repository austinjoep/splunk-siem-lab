# Splunk SIEM Lab

This repository documents an end-to-end SOC-style lab where I built a Splunk pipeline, simulated attacker activity from Kali Linux, detected the activity in Windows Security logs, and performed containment on the target host.

## Project Overview

- **Objective:** Detect brute-force and RDP-based attacker activity in near real time.
- **SIEM Platform:** Splunk Enterprise `9.4.3` on Kali Linux.
- **Log Source:** Windows Server 2022 Security Event Logs via Splunk Universal Forwarder `9.4.0`.
- **Attack Simulation:** Hydra brute-force against RDP, followed by successful remote login.
- **Detection Focus:** Event correlation across failed login (`4625`), successful login (`4624`), and network/firewall activity (`5156`, `5157`, `5158`).

## Lab Architecture

- **Kali Linux (SIEM/Attacker):** `10.0.2.5`
- **Windows Server 2022 (Victim/Log source):** `10.0.2.15`
- **Forwarder Output Port:** `9997`
- **Splunk Web:** `http://10.0.2.5:8000`

```text
Kali Linux (10.0.2.5)
  ├─ Recon: netdiscover, nmap
  ├─ Attack: hydra, xfreerdp
  └─ Splunk Enterprise 9.4.3 (SIEM)
            ▲
            │ Forwarded Windows Security logs (9997)
            │
Windows Server 2022 (10.0.2.15)
  ├─ Splunk Universal Forwarder 9.4.0
  └─ Security Events: 4625, 4624, 4672, 5156/5157/5158
```

## Repository Contents

- `docs/lab-rebuild-guide.md` - Daily reset rebuild instructions for Splunk + Forwarder.
- `docs/capstone-presentation-summary.md` - Structured write-up of the capstone presentation.
- `docs/detection-queries.md` - SPL queries and alert logic used in the lab.
- `recon/netdiscover_nmap.md` - Reconnaissance workflow and interpretation.
- `attack/hydra-rdp-bruteforce.md` - Brute-force simulation procedure.
- `attack/xfreerdp-access-validation.md` - Post-compromise RDP validation.
- `defense/firewall-containment.md` - Windows firewall containment playbook.
- `reporting/incident-report.md` - SOC-style incident report template filled with this lab scenario.

## Attack Timeline (High Level)

1. **Recon:** Attacker identifies live host and exposed RDP service.
2. **Access Attempt:** Hydra generates repeated failed logons (`4625`).
3. **Compromise Signal:** Successful remote logon (`4624`, type `10`) appears after failures.
4. **Privilege Context:** Privileged assignment events (`4672`) validate account impact.
5. **Containment:** Source IP is blocked at host firewall; blocked traffic evidence is monitored (`5157`).

## Skills Demonstrated

- SIEM deployment and log onboarding
- Windows Security Event monitoring
- Brute-force simulation and attack chain mapping
- Detection engineering with SPL
- Alert design and response validation
- Basic containment through host firewall controls

## Important Notes

- This repository is for educational and defensive security lab use only.
- Any credentials shown in lab notes should be replaced before using in other environments.
- Only test these techniques in systems you own or are explicitly authorized to assess.
- IP addresses, usernames, and passwords in this repo are lab-only values.
