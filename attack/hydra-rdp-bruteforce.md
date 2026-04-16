# Attack Simulation: Hydra RDP Brute Force

## Objective

Generate realistic failed logon activity against RDP so Splunk can detect brute-force behavior via `EventCode=4625`.

## Prerequisites

- Remote Desktop enabled on Windows target
- Target account permitted for RDP access
- Splunk Forwarder actively shipping Security logs
- Password list prepared on Kali

## Tool Installation

```bash
sudo apt update
sudo apt install hydra -y
```

## Password List Example

```bash
nano ~/passwords.txt
```

Include representative weak/common values plus one known lab credential for controlled success verification.

## Hydra Command

```bash
hydra -l Administrator -P ~/passwords.txt -t 4 -V rdp://10.0.2.15
```

## Command Parameters

| Flag | Meaning |
|---|---|
| `-l` | Single username target |
| `-P` | Password wordlist file |
| `-t 4` | Concurrent attempts |
| `-V` | Verbose output |
| `rdp://` | RDP protocol module |

## Expected Observables

- Repeated failed authentication attempts
- High-volume `4625` events in short windows
- Single source IP repeatedly targeting one account

## Detection Tie-In

Use these docs for related validation:

- `docs/detection-queries.md`
- `docs/lab-rebuild-guide.md`

## Lab Safety Note

Run this only in isolated lab environments you own or are authorized to test.
