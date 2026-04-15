# Capstone Presentation Summary

This document captures the core narrative and technical flow from the capstone presentation: **Threat Detection using Splunk**.

## Executive Summary

The capstone demonstrates how an attacker can move from reconnaissance to successful remote access, and how defenders can detect and contain each phase using Splunk and host-based controls.

## Objectives

- Simulate realistic attacker behavior in a controlled lab
- Centralize Windows logs into Splunk
- Detect high-risk authentication patterns
- Correlate events into a timeline and trigger alerts
- Apply containment through firewall blocking and validate impact

## Environment

- Kali Linux attacker/detection host
- Windows Server 2022 victim/log source
- Splunk Enterprise + Universal Forwarder integration

## Attack Chain (Kill Chain Mapping)

### 1) Reconnaissance

- Passive and active network discovery were used (`netdiscover`, `nmap`)
- Target host and exposed RDP service were identified

### 2) Weaponization and Delivery

- A dictionary password list was prepared
- RDP became the protocol channel for login attempts

### 3) Exploitation

- Hydra executed repeated RDP login attempts
- Brute-force behavior produced a high volume of failed logons

### 4) Installation / Access Establishment

- Successful credentials enabled GUI-level remote access via `xfreerdp`
- Activity transitioned from attack noise to valid-session behavior

### 5) Actions on Objectives

- With remote access established, attacker post-access opportunities included:
  - Privilege escalation
  - Credential access/lateral movement
  - Persistence
  - Data exfiltration
  - Malware simulation

### 6) Defender Response

- Detection rules correlated suspicious source IP behavior over time
- Firewall rules blocked malicious inbound traffic
- Block efficacy was validated through subsequent log evidence

## Detection Strategy in Splunk

- Monitor failed authentication bursts
- Detect successful login following repeated failures
- Track remote interactive logon patterns
- Visualize suspicious trends with dashboards and alerting

## Event IDs Emphasized

- `4625` - Failed logon
- `4624` - Successful logon
- `5156`, `5157`, `5158` - Windows Filtering Platform connection and block telemetry

## MITRE ATT&CK Mapping (As Presented)

- `T1021.001` - Remote Services: RDP
- `T1078` - Valid Accounts

## Reporting and Lessons Learned

- Document full attack timeline
- Preserve Indicators of Compromise (IOCs)
- Capture affected assets and methods used
- Include screenshots and dashboard evidence
- Recommend practical mitigation and hardening actions

The key takeaway: simple, repeatable log correlation in Splunk can quickly expose brute-force and post-compromise behavior when telemetry is onboarded correctly.
