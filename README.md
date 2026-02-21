# windows-authentication-monitoring-lab

Main objective of this project is to simulate authentication attacks against a Windows system and detect them using Splunk SIEM.


## Lab Architecture
- Host: Windows 11
- VirtualBox
- Kali Linux (Attacker)
- Windows 10 (Target)
- Splunk Enterprise (SIEM)
- Host-Only Network (192.168.56.0/24)

## Attack Simulation - Local Failed Login
- Entered incorrect password multiple times
- Generated EventCode 4625 (LogonType 2)
![incorrectpass](labscreenshots/incorrect_pass_wind.png)


## Remote Authentication Attempt (Kali → Windows)

- smbclient -L //192.168.56.102 -U wisdom%wrongpass

- Generated:
- EventCode 4625
- LogonType 3
- Source IP: 192.168.56.101

![kali](labscreenshots/kali_auth_attempt.png)


## Log Detection in Splunk
- Failed Login Detection
![eventcode](labscreenshots/event_code_4625.png)
- EventCode=4625

## Brute Force Detection Logic
- EventCode=4625 LogonType=3
| stats count by aIpAddress
| where count > 5

![eventcode](labscreenshots/brute_force.png)

## Kali IP address detected:
![eventcode](labscreenshots/kali_ip.png)

## Account Lockout Detection
- EventCode=4740


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


## MITRE ATT&CK Mapping

| Tactic                 | Technique                 | ID     | Description                              |
|------------------------|--------------------------|--------|------------------------------------------|
| Credential Access      | Brute Force              | T1110  | Repeated failed authentication attempts  |
| Discovery              | Network Service Discovery| T1046  | Nmap scanning from Kali                  |
| Discovery              | Network Share Discovery  | T1135  | SMB enumeration attempts                 |








