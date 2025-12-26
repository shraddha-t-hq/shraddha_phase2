# YARA Rules - YARA mean one!

## Challenge Overview
This challenge introduces YARA as a defensive tool used to detect malicious patterns and hidden indicators inside files. 
The objective was to write a custom YARA rule to search through a directory of files and identify a hidden message left by McSkidy. 
The task focused on scanning image files for strings starting with TBFC: followed by alphanumeric characters, extracting those fragments, and reconstructing the final message.

## Learnings
From this challenge, I learned:
- YARA is used to detect patterns in files, memory, and directories
- Custom YARA rules that allow defenders to search for very specific indicators
- Regular expressions which are useful when patterns are similar but not identical

## My Solve 
I first created a YARA rule file named `tbfc_message.yar` using nano and defined a string to match `TBFC:` followed by alphanumeric characters.<br/>
I navigated to the `/home/ubuntu/Downloads/easter` directory and ran the command `yara -r -s tbfc_message.yar /home/ubuntu/Downloads/easter`.<br/>
Initially, I encountered file path errors because the rule file was not in the correct directory, so I recreated it directly inside the target folder. <br/>
After running the scan again, YARA matched multiple image files and displayed the extracted strings using the `-s` flag. <br/>
Each image contained a fragment of the hidden message such as `TBFC:Find`, `TBFC:me`, `TBFC:in`, `TBFC:HopSec`, and `TBFC:Island`.<br/>
By combining these fragments in order, I reconstructed the full message sent by McSkidy: `“Find me in HopSec Island.”`<br/>
<br/>
**Example of one script written for the first challenge :**
```
rule TBFC_Message_Hunt
{
    strings:
        $msg = /TBFC:[A-Za-z0-9]+/ nocase

    condition:
        $msg
}

```
<br/>
**Terminal Working :**
```
ubuntu@tryhackme:~$ nano tbfc_message.yar
ubuntu@tryhackme:~$ cd /home/ubuntu/Downloads/easter 
ubuntu@tryhackme:~/Downloads/easter$ yara -r -s tbfc_message.yar /home/ubuntu/Downloads/easter
error: could not open file: tbfc_message.yar
ubuntu@tryhackme:~/Downloads/easter$ mv tbfc_message /home/ubuntu/Downloads/easter
mv: cannot stat 'tbfc_message': No such file or directory
ubuntu@tryhackme:~/Downloads/easter$ mv tbfc_message.yar /home/ubuntu/Downloads/easter
mv: cannot stat 'tbfc_message.yar': No such file or directory
ubuntu@tryhackme:~/Downloads/easter$ nano tbfc_message.yar
ubuntu@tryhackme:~/Downloads/easter$ yara -r -s tbfc_message.yar /home/ubuntu/Downloads/easter
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/tbfc_message.yar
0x5:$msg: TBFC
0x36:$msg: TBFC
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter46.jpg
0x2f78a:$msg: TBFC
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/embeds
0x18f:$msg: TBFC
0x1bf:$msg: TBFC
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter16.jpg
0x3bb7f7:$msg: TBFC
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter10.jpg
0x137da8:$msg: TBFC
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter52.jpg
0x2a2ad2:$msg: TBFC
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter25.jpg
0x42c778:$msg: TBFC



ubuntu@tryhackme:~/Downloads/easter$ nano tbfc_message.yar
ubuntu@tryhackme:~/Downloads/easter$ yara -r -s tbfc_message.yar /home/ubuntu/Downloads/easter
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter46.jpg
0x2f78a:$msg: TBFC:HopSec
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter10.jpg
0x137da8:$msg: TBFC:Find
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter16.jpg
0x3bb7f7:$msg: TBFC:me
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter52.jpg
0x2a2ad2:$msg: TBFC:Island
TBFC_Message_Hunt /home/ubuntu/Downloads/easter/easter25.jpg
0x42c778:$msg: TBFC:in
```

## Tools Used 
- YARA

## Flag / Answers
- Number of images containing TBFC: **5**
- Regex used: **/TBFC:[A-Za-z0-9]+/**
- Message from McSkidy: **Find me in HopSec Island**
