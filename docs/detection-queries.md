# Detection Queries (Splunk SPL)

Use these queries to validate ingestion, detect brute-force behavior, and monitor suspicious RDP success patterns.

## 1) Ingestion Health Check

```spl
index=*
| stats count by index, sourcetype
```

## 2) Windows Security Events Only

```spl
index="main" sourcetype="WinEventLog:Security"
| stats count by EventCode
| sort - count
```

## 3) Failed RDP Logons (EventCode 4625)

```spl
index="main" sourcetype="WinEventLog:Security" EventCode=4625
| eval src_ip=coalesce(IpAddress, Source_Network_Address, WorkstationName)
| stats count as failed_attempts values(Account_Name) as users by src_ip, host
| sort - failed_attempts
```

## 4) Burst Detection: 5+ Fails in 5 Seconds

```spl
index="main" sourcetype="WinEventLog:Security" EventCode=4625
| bucket _time span=5s
| eval src_ip=coalesce(IpAddress, Source_Network_Address, WorkstationName)
| stats count as failed_attempts values(Account_Name) as targeted_accounts by _time, src_ip, host
| where failed_attempts >= 5
| sort - _time
```

## 5) Successful RDP Logons (Type 10)

```spl
index="main" sourcetype="WinEventLog:Security" EventCode=4624 Logon_Type=10
| eval src_ip=coalesce(IpAddress, Source_Network_Address, WorkstationName)
| table _time, host, Account_Name, Logon_Type, src_ip
| sort - _time
```

## 6) Success After Repeated Failures (Correlation)

```spl
index="main" sourcetype="WinEventLog:Security" (EventCode=4625 OR EventCode=4624)
| eval src_ip=coalesce(IpAddress, Source_Network_Address, WorkstationName)
| eval is_fail=if(EventCode=4625,1,0), is_success=if(EventCode=4624 AND Logon_Type=10,1,0)
| transaction src_ip maxspan=15m
| eval fail_count=mvcount(mvfilter(EventCode=4625)), success_count=mvcount(mvfilter(EventCode=4624))
| where fail_count>=5 AND success_count>=1
| table _time, duration, src_ip, fail_count, success_count, host, Account_Name
| sort - _time
```

## 7) Firewall Block Verification (EventCode 5157)

```spl
index="main" sourcetype="WinEventLog:Security" EventCode=5157
| eval src_ip=coalesce(SourceAddress, IpAddress)
| stats count values(DestPort) as dest_ports values(Application) as apps by src_ip, host
| sort - count
```

## Suggested Alerts

- **Brute-force window:** Trigger when EventCode `4625` reaches >= 5 from same source in 5 seconds.
- **Compromise pattern:** Trigger when successful remote logon (`4624`, type 10) follows repeated failures from same source.
- **Blocked attacker persistence:** Trigger on repeated `5157` events from known malicious source IPs.
