# Forensics - Registry Furensics

## Challenge Overview
This challenge introduces Windows Registry forensics by analyzing offline registry hives from a compromised Windows system (dispatch-srv01). 
The goal is to understand how Windows stores configuration and activity data in registry hives and to extract forensic evidence related to program installation, 
execution, and persistence using the Registry Explorer tool.

## Learnings
From this challenge, I learned:
- The Windows Registry acts as the operating system’s configuration database, storing system-wide and user-specific information.
- Registry data is stored in multiple binary hive files, not a single location.
- The purpose and forensic value of major registry hives:
  - SYSTEM – hardware, services, boot configuration
  - SOFTWARE – installed applications, autostarts
  - SAM – user accounts and metadata
  - NTUSER.DAT – user activity, recent files, execution traces
- Why offline registry analysis is essential in forensics (to avoid modifying evidence).
- How Registry Explorer parses binary registry data and transaction logs safely.
- How attackers commonly use registry Run keys to maintain persistence across reboots.
  
## My Solve 
To administer suspecious activity on a particular application on 21st, I opened the SOFTWARE hive and followed this path : *Microsoft -> Windows -> Current Version -> Uninstall*
There I found the application that was tampered with on 21st.
<img width="1920" height="1064" alt="Screenshot 2025-12-22 173950" src="https://github.com/user-attachments/assets/594008c6-4dff-4d98-837f-d60c5825c8ed" />

Then I opened the NTUSER.DAT hive and went to UserAssist 
There I found the program/process name containing DroneManager.
<img width="1920" height="1065" alt="Screenshot 2025-12-22 174308" src="https://github.com/user-attachments/assets/40bd7183-ef31-4b82-90a0-685e830a1751" />

I opened the SOFTWARE hive again and followed this path : *Microsoft -> Windows -> Current Version -> Run*
There I found the drone_helper command, the `Data` column had the answer.
<img width="1920" height="1066" alt="Screenshot 2025-12-22 174608" src="https://github.com/user-attachments/assets/add84d95-e339-4b02-a541-b62558b553a0" />

## Flag / Answers
- Application installed before abnormal activity: **DroneManager Updater**
- Full path from which the application was launched: **C:\Users\dispatch.admin\Downloads\DroneManager_Setup.exe**
- Registry value added for persistence: **C:\Program Files\DroneManager\dronehelper.exe" --background**
