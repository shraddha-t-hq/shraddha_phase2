# Passwords - A Cracking Christmas

## Challenge Overview
This challenge focused on understanding how attackers recover passwords protecting encrypted files without breaking encryption itself.
Instead of attacking cryptographic algorithms directly, attackers target weak or predictable passwords using techniques such as dictionary attacks and brute-force / mask attacks.

The practical objective was to identify encrypted files, choose the correct cracking approach based on file type, and recover the passwords to extract the hidden flags.

## Learnings
From this challenge, I learned:
- Attackers rarely attack encryption directly, they attack password choice
- The difference between:
  - Dictionary attacks (testing known/common passwords)
  - Brute-force attacks (trying all combinations)
  - Mask attacks (brute force with known structure)
- Why leaked password lists like `rockyou.txt` are so effective
- How knowing the file type(like zip and pdf) determines the cracking approach
- How tools like `john` and `pdfcrack` abstract complex cracking logic
- That offline password cracking is stealthy because it:
  - Does not generate failed login attempts
  - Does not trigger account lockouts

## My Solve 
**The following are the snapshots of all the processes and terminal workings I did :**
**PDF exploitatioin:**
<img width="1920" height="1065" alt="Screenshot 2025-12-21 012446" src="https://github.com/user-attachments/assets/f2d84353-9dc6-4979-a5a6-24d5bfb6e7b4" />
<img width="1920" height="1068" alt="Screenshot 2025-12-21 013114" src="https://github.com/user-attachments/assets/c306ec96-3173-411d-a13f-5848364d5033" />
**ZIP exploitation:**
<img width="1920" height="409" alt="Screenshot 2025-12-21 012951" src="https://github.com/user-attachments/assets/34a67d17-15cb-4c57-9e7f-a0bb2c854ef2" />
<img width="1920" height="1066" alt="Screenshot 2025-12-21 014545" src="https://github.com/user-attachments/assets/ddb48789-3b3b-409e-a7fc-11a8c8e9e4b8" />

## Commands used :
- file – to identify encrypted file types
- pdfcrack – dictionary attack against encrypted PDFs
- john (John the Ripper) – flexible password cracking framework
- zip2john – converting ZIP encryption into crackable hash format
- rockyou.txt – leaked password wordlist

## Flag / Answers :
```
THM{Cr4ck1ng_PDFs_1s_34$y}
THM{Cr4ck1n6_z1p$_1s_34$yyyy}
```
