# Splunk SIEM Lab

This repository documents my end-to-end Splunk SIEM capstone lab, where I simulated attacker behavior against a Windows server and detected it in Splunk using Windows Security logs.

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

## Repository Contents

- `docs/lab-rebuild-guide.md` - Daily reset rebuild instructions for Splunk + Forwarder.
- `docs/capstone-presentation-summary.md` - Structured write-up of the capstone presentation.
- `docs/detection-queries.md` - SPL queries and alert logic used in the lab.

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
