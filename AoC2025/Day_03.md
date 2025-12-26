# Splunk Incident Investigation – King Malhare Attack

## Challenge Overview
This challenge focused on investigating a full-scale web attack using Splunk logs.
Pre-ingested web server and firewall logs were provided to analyse abnormal traffic, identify the attacker, trace the attack stages, and confirm post-exploitation activity such as data exfiltration and C2 communication.
The objective was to reconstruct the attacker’s behaviour from reconnaissance to ransomware execution.

## Learnings
From this challenge, I learned:
- How to triage incidents using **Splunk indexes and sourcetypes**
- How to detect attack windows using **timechart visualisation**
- How malicious activity stands out through abnormal **user_agent**, **path**, and **client_ip** values
- How attackers chain multiple techniques in a single intrusion
- How payloads and request parameters reveal the attacker’s intent
- How to correlate **web logs** with **firewall logs** to confirm compromise and C2 activity
- How SPL commands like `stats`, `sort`, and field filtering support investigations

## My Solve
I started by querying all web traffic logs and used a timechart to identify a clear spike in activity, which marked the main attack window. 
I then analysed key fields like `user_agent`, `client_ip`, and `path` and filtered out legitimate browser traffic. This revealed a single attacker IP responsible for all suspicious requests. 
Tracing this IP chronologically showed reconnaissance attempts targeting sensitive files using payloads such as `/.env`, `/.git`, and `phpinfo.php`, followed by enumeration using path traversal payloads like `../../etc/passwd` and redirect-based probes. 
The attacker then launched SQL injection attacks using automated tools like **sqlmap** and **Havij**, confirmed by payloads containing `SLEEP(5)` that caused delayed server responses. 
After exploitation, the attacker attempted to download sensitive archives such as `backup.zip` and `logs.tar.gz`. Finally, successful remote code execution was confirmed through webshell payloads like `shell.php?cmd=./bunnylock.bin`, indicating ransomware staging. 
This compromise was validated by pivoting to firewall logs, which showed outbound C2 communication and large data transfers from the compromised server to the attacker IP.

## Tools Used
- **Splunk Search & Reporting**
- Splunk Search Processing Language (SPL)
  - `timechart`
  - `stats`
  - `sort`
  - Field-based filtering
- Web traffic logs (`web_traffic`)
- Firewall logs (`firewall_logs`)

## Flag / Answers
- **Attacker IP compromising the web server:** `198.51.100.55`
- **Peak traffic day (YYYY-MM-DD):** `2025-10-12`
- **Count of Havij user_agent events:** `993`
- **Number of path traversal attempts observed:** `658`
- **Total bytes transferred to the C2 server:** `126167`
