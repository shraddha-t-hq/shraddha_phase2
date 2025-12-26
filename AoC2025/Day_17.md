# CyberChef - Hoperation Save McSkidy

## Challenge Overview
This challenge focused on understanding and exploiting CyberChef - a helpful encrypting and decrypting tool.
It was required to crack multiple gates to get the final flag.

## Learnings
From this challenge, I learned:
- How to use CyberChef.
- Revision of the different cypher techniques like - ROT13, Base 64, XOR

## My Solve 
**First Lock - Outer Gate**
To crack the first gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level1` which revealed the question I could ask the Bunnygram.
The Bunnygram only accepted Base64 encrypted text.
Then for Username, I encrypted `CottonTail`-the guard name into base64, for password, the Bunnygram provided the encrypted password which had to be further decrypted
**Password of first level : Iamsofluffy**

**Second Lock - Outer Wall**
To crack the second gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level2` which revealed the correct question to ask the Bunnygram.
Then for `Username`, I encrypted `CarrotHelm`- the guard name into base64, for password, the Bunnygram provided the encrypted password which had to be further decrypted.
**Password of second level : Itoldyoutochangeit!**

**Third Lock - Guard House**
To crack the third gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level3` which revealed the XOR decryption key.
Then for `Username`, I encrypted `LongEars`- the guard name into base64, for password, it was required to ask a simple question to the bunnygram, so I entered as payload, the encrypted version of `Password please.`, the Bunnygram provided the encrypted password which had to be further decrypted using the XOR decryption key.(`cyberchef`)
**Password of third level : BugsBunny**

**Fourth Lock - Inner Castle**
To crack the third gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level4`.
Then for `Username`, I encrypted `Lenny`- the guard name into base64, for password, it was required to ask a simple question to the bunnygram, so i entered as payload, the encrypted version of `Password please.` again. The Bunnygram provided the encrypted password which was a hash from which i extracted `passw0rd1` using an online site.
**Password of fourth level : passw0rd1**

**Fifth Lock - Prison Tower**
To crack the third gate, I first went to the *Inspect -> Network tab -> Refresh*. This revealed a file, `level5` which revealed the number of decryption process to follow(`R2`). Then I went to `app.js` (in `Storage`) to see the decryption scheme.
Then for `Username`, I encrypted `Carl`- the guard name into base64, for password, it was required to ask a simple question to the bunnygram, so I entered as payload, the encrypted version of `Password please.`, the Bunnygram provided the encrypted password which had to be further decrypted using the scheme - *Base64 -> Hex -> Reverse*
**Password of fifth level : 51rBr34chBl0ck3r**

## Tools Used 
- CyberChef

## Flag / Answers
- Final Flag
  ```
  THM{M3D13V4L_D3C0D3R_4D3P7}
  ```
