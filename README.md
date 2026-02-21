# Windows-authentication-monitoring-lab

This project simulates authentication-based attacks against a Windows system and demonstrates detection and monitoring using Splunk SIEM.

## Lab Architecture
- Host: Windows 11
- VirtualBox
- Kali Linux (Attacker)
- Windows 10 (Target)
- Splunk Enterprise (SIEM)
- Host-Only Network (192.168.56.0/24)

## Attack Simulation - Local Failed Login
- Entered incorrect password multiple times
- Generated:
```spl
EventCode=4625
| stats count by LogonType
```
![incorrectpass](labscreenshots/incorrect_pass_wind.png)


## Remote Authentication Attempt (Kali → Windows)

- smbclient -L //192.168.56.102 -U wisdom%wrongpass

- Generated:
```spl
EventCode=4625 LogonType=3
| stats count by aIpAddress
| sort - count
```

![kali](labscreenshots/kali_auth_attempt.png)


## Log Detection in Splunk
- Failed Login Detection
![eventcode](labscreenshots/event_code_4625.png)
```spl
EventCode=4625
```

## Brute Force Detection Logic
```spl
EventCode=4625 LogonType=3
| stats count by aIpAddress
| where count > 5
```

![eventcode](labscreenshots/brute_force.png)

## Kali IP address detected:
![eventcode](labscreenshots/kali_ip.png)

## Account Lockout Detection
```spl
EventCode=4740
```

![eventcode](labscreenshots/user_locked_out.png)
![eventcode](labscreenshots/lockout_code.png)

## Alert Configuration
Configured Splunk alert:
Trigger: Results > 0
Time Range: Last 15 minutes
Schedule: Every 15 minutes

![eventcode](labscreenshots/alert_config.png)


## Dashboard Monitoring
- Created authentication monitoring dashboard showing login spikes and source IP activity.

![eventcode](labscreenshots/monitor_dashboard.png)


## Key Findings

- Successfully detected both local and remote failed authentication attempts.
- Identified attacker source IP (192.168.56.101).
- Triggered and monitored account lockout events (EventCode 4740).
- Implemented automated brute-force detection alert.

## MITRE ATT&CK Mapping

| Tactic                 | Technique                 | ID     | Description                              |
|------------------------|--------------------------|--------|------------------------------------------|
| Credential Access      | Brute Force              | T1110  | Repeated failed authentication attempts  |
| Discovery              | Network Service Discovery| T1046  | Nmap scanning from Kali                  |
| Discovery              | Network Share Discovery  | T1135  | SMB enumeration attempts                 |


## Skills Demonstrated

- SIEM configuration (Splunk Enterprise)
- Windows Event Log ingestion
- Authentication monitoring
- Detection engineering using SPL
- Brute-force attack identification
- Alert automation
- SOC-style investigation workflow





