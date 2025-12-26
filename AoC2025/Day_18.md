# Obfuscation - The Egg Shell File

## Challenge Overview
This challenge introduces common obfuscation techniques used by attackers to hide malicious content and evade detection mechanisms. 
The focus is on recognizing visual and structural patterns in obfuscated data, understanding how simple and layered obfuscation works, and reversing or applying these techniques using practical tools
The challenge walks through basic ciphers such as ROT variants, more complex byte-level obfuscation using XOR, and real-world layered obfuscation scenarios combining compression, encoding, and encryption-like operations.

## Learnings
From this challenge, I learned:
- Obfuscation is commonly used by attackers to delay analysis and bypass basic security controls.
- Simple substitution ciphers like ROT1 and ROT13 preserve spacing and word structure, making them visually detectable.
- XOR obfuscation operates at the byte level, producing non-readable characters while preserving string length.
- Base64 encoding has recognizable character patterns and padding (= or ==).
- Layered obfuscation must be reversed in the opposite order of how it was applied.
- CyberChef is a powerful tool for both identifying and reversing common obfuscation techniques.
- The Magic operation in CyberChef can assist in identifying unknown encodings but is not foolproof.

## My Solve 
The following images show how I solved this challenge :

<img width="1920" height="1128" alt="Screenshot 2025-12-22 181532" src="https://github.com/user-attachments/assets/a550b1e9-7f4c-4072-b363-415b94de0565" />
<img width="1920" height="1128" alt="Screenshot 2025-12-22 182257" src="https://github.com/user-attachments/assets/945de3f0-8f36-48be-8665-faae00017ade" />
<img width="1920" height="1128" alt="Screenshot 2025-12-22 182412" src="https://github.com/user-attachments/assets/35ce8278-0d5c-49f8-9bb9-4db9bbe59276" />
<img width="1920" height="1128" alt="Screenshot 2025-12-22 183123" src="https://github.com/user-attachments/assets/b182a2e5-a981-4b7a-a7ce-79b00f28de56" />
<img width="1920" height="1128" alt="Screenshot 2025-12-22 184046" src="https://github.com/user-attachments/assets/a7a68e9b-c7ba-49bd-8025-033bb9de75b9" />


## Tools Used 
- CyberChef
## Flag / Answers
Flags :
```
THM{C2_De0bfuscation_29838}
THM{API_Obfusc4tion_ftw_0283}
```
