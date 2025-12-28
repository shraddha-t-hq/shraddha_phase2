# IDOR - Santa’s Little IDOR

## Challenge Overview
This challenge focused on using **RITA (Real Intelligence Threat Analytics)** to detect command-and-control (C2) activity from captured network traffic. 
The objective was to analyze a provided PCAP file, convert it into Zeek logs, import those logs into RITA, and then identify malicious infrastructure, communication patterns, and indicators of compromise (IoCs).

## Learnings
From this challenge, I learned:
- How RITA detects C2 traffic using behavioral analytics instead of signatures
- The importance of Zeek logs as structured input for threat hunting
- How threat modifiers like prevalence, beacon score, rare signature, and first seen help prioritize suspicious connections
- How to use RITA’s interactive TUI, search filters, and sorting to efficiently investigate suspicious activity

## My Solve 
The following is how I solved this challenge :
1. Converted PCAP to Zeek logs : Used Zeek to parse the provided PCAP into structured logs using `zeek readpcap pcaps/rita_challenge.pcap zeek_logs/rita_challenge`
2. Imported Zeek logs into RITA : Imported and analyzed the dataset using - `rita import --logs ~/zeek_logs/rita_challenge/ --database rita_challenge`
3. Viewed and analyzed results : Opened the RITA interface using - `rita view rita_challenge`
Then I Analysed the RITA registry to get answers to all questions :
To solve the first question it was just required to count the number of hosts.
Scrolling through them the highest number of connections was found to be 40.
<img width="1920" height="1070" alt="Screenshot 2025-12-24 121235" src="https://github.com/user-attachments/assets/918c120c-7d92-427c-a90c-dca1e0d1555d" />
I used the following payload to filter through the connections according to the criterion given :
`dst:rabbithole.malhare.net beacon:>=70 sort:duration-desc`
<img width="1920" height="1128" alt="Screenshot 2025-12-24 121536" src="https://github.com/user-attachments/assets/8996e8ab-5cf6-4381-81e7-78452cbb8c78" />

## Tools Used 
- RITA – Threat analytics and C2 detection
- Zeek – PCAP parsing and network log generation

## Flag / Answers
- How many hosts are communicating with malhare.net? : **6**
- Which Threat Modifier tells us the number of hosts communicating to a certain destination? : **prevalence**
- What is the highest number of connections to rabbithole.malhare.net? : **40**
- Which search filter would you use to search for all entries that communicate to rabbithole.malhare.net with a beacon score greater than 70% and sorted by connection duration (descending)? :
  **dst:rabbithole.malhare.net beacon:>=70 sort:duration-desc**
- Which port did the host 10.0.0.13 use to connect to rabbithole.malhare.net? : **80**
