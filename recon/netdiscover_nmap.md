# Reconnaissance Phase: Host and Service Discovery

## Goal

Identify reachable hosts in the lab subnet and confirm whether Windows Remote Desktop (`3389`) is exposed before launching authentication attacks.

## Environment Context

- Attacker host: Kali Linux (`10.0.2.5`)
- Target host: Windows Server 2022 (`10.0.2.15`)
- Recon tools: `netdiscover`, `nmap`

## Step 1: Discover Active Hosts

```bash
netdiscover -r 10.0.2.0/24 -P
```

### Why this matters

- Quickly maps live systems in the subnet
- Helps validate target IP after VM resets
- Provides baseline network visibility prior to scanning

## Step 2: Enumerate Open Services on Target

```bash
nmap -sS -T4 -p- 10.0.2.15
```

### Option breakdown

| Option | Purpose |
|---|---|
| `-sS` | SYN scan (fast reconnaissance of open ports) |
| `-T4` | Higher timing template for quicker results in lab conditions |
| `-p-` | Scans all TCP ports instead of default top ports |

## Key Finding

The target exposed RDP (`3389/tcp`), which validated the brute-force simulation path used later in the lab.

## Defensive Relevance

Recon data should feed monitoring strategy:

- Prioritize telemetry for externally reachable management ports
- Alert on unusual scan behavior against administrative services
- Enforce segmentation and host firewall policy for RDP/WinRM exposure

## ATT&CK Alignment (Conceptual)

- Active scanning and service discovery are pre-compromise discovery behaviors that inform attacker targeting and execution decisions.
