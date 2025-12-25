# CyberChef - Hoperation Save McSkidy

## Challenge Overview
This challenge focused on understanding and exploiting CyberChef - a helpful encrypting and decrypting tool.
It was required to crack multiple gates to get the final flag.

## Learnings
From this challenge, I learned:
- How to use CyberChef.
- Revision of the different cypher techniques like - ROT13, Base 64, XOR

## My Solve 
**First Gate**
To crack the first gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level1` which revealed the question I could ask the Bunnygram.
The Bunnygram only accepted Base64 encrypted text.
Then for Username, I encrypted `CottonTail`-the guard name into base64, for password, the Bunnygram provided the encrypted password which had to be further decrypted
**Password of first level : Iamsofluffy**

**Second Gate**
To crack the second gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level2` which revealed the correct question to ask the Bunnygram.
Then for `Username`, I encrypted `CarrotHelm`- the guard name into base64, for password, the Bunnygram provided the encrypted password which had to be further decrypted.
**Password of second level : Itoldyoutochangeit!**

**Third Gate**
To crack the third gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level3` which revealed the XOR decryption key.
Then for `Username`, I encrypted `LongEars`- the guard name into base64, for password, the Bunnygram provided the encrypted password which had to be further decrypted.
**Password of third level : Itoldyoutochangeit!**


## Tools Used 
- Browser Developer Tools
  - Network tab
  - Application / Storage tab (Local Storage)
- Manual parameter tampering
- Logical observation of server responses

## Flag / Answers
- What does IDOR stand for? : **Insecure Direct Object Reference**
- What type of privilege escalation are most IDOR cases? : **Horizontal**
- User ID of the parent with 10 children: **15**
