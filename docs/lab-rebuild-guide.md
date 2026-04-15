# Lab Rebuild Guide (Daily Reset)

This guide rebuilds the Splunk SIEM lab used for threat detection testing.

## Lab Details

- **Kali Linux:** `10.0.2.5` (Splunk Enterprise `9.4.3`)
- **Windows Server 2022:** `10.0.2.15` (Universal Forwarder `9.4.0`)
- **Goal:** Ingest Windows Security logs and detect failed/successful RDP activity in Splunk
- **Estimated rebuild time:** 12 to 15 minutes

## 1) Install Splunk Enterprise on Kali

Run as `kaliuser` (use `sudo` as shown):

```bash
sudo apt update
wget -O splunk.tgz https://download.splunk.com/products/splunk/releases/9.4.3/linux/splunk-9.4.3-237ebbd22314-linux-amd64.tgz
sudo tar -xvzf splunk.tgz -C /opt
cd /opt/splunk/bin
sudo ./splunk start --accept-license
```

Set Splunk admin credentials when prompted, then configure receiving:

```bash
sudo ./splunk enable listen 9997 -auth <splunk_user>:<splunk_password>
sudo ss -tuln | grep 9997
sudo ./splunk restart
```

Open Splunk Web:

- `http://10.0.2.5:8000`

## 2) Install Splunk Universal Forwarder on Windows Server

Download:

- [Splunk Universal Forwarder 9.4.0 (Windows x64 MSI)](https://download.splunk.com/products/universalforwarder/releases/9.4.0/windows/splunkforwarder-9.4.0-6b4ebe426ca6-windows-x64.msi)

Installer values:

- Accept license
- Local admin for forwarder service account
- Deployment Server: leave blank
- Receiving indexer: `10.0.2.5:9997`

## 3) Configure `inputs.conf` (Critical)

Create or update:

- `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`

Content:

```ini
[WinEventLog://Security]
disabled = false
start_from = oldest
current_only = false
index = main
```

Restart forwarder:

```cmd
cd "C:\Program Files\SplunkUniversalForwarder"
net stop SplunkForwarder && net start SplunkForwarder
```

## 4) Verification Checklist

On Windows (admin terminal):

```cmd
ping 10.0.2.5
telnet 10.0.2.5 9997
```

If Telnet is missing:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName TelnetClient -All
```

Additional checks:

```cmd
cd "C:\Program Files\SplunkUniversalForwarder\bin"
splunk status
type "C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log" | findstr /i "Connected to"
```

Expected indicator:

- `Connected to idx=10.0.2.5:9997`

## 5) Run Detection Searches in Splunk

```spl
index=*
```

```spl
index="main" sourcetype="WinEventLog:Security"
```

## 6) Simulate Brute-Force and RDP Access

On Kali, install tools:

```bash
sudo apt install hydra -y
sudo apt install freerdp3-x11 -y
```

Hydra brute-force simulation:

```bash
hydra -l Administrator -P ~/passwords.txt -t 4 -V rdp://10.0.2.15
```

Remote login test:

```bash
xfreerdp /u:Administrator /p:'<password>' /v:10.0.2.15
```

## 7) Common Daily Fixes

- **No events in Splunk:** Rebuild `inputs.conf` and restart forwarder.
- **Port 9997 not listening on Kali:** Re-run `enable listen 9997` and restart Splunk.
- **"No stanzas found" log issue:** Validate `inputs.conf` stanza syntax and path.
- **Connection troubleshooting:** Validate ping/telnet, then inspect `splunkd.log`.

## 8) Defender Preparation / Containment Context

To support this lab scenario:

- Enable Remote Desktop on Windows
- Confirm RDP listener on `3389`
- Allow/assign remote access permissions for intended account

After malicious behavior is identified, apply an inbound Windows Firewall block for attacker source IP and confirm enforcement events in logs.
