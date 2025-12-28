# ICS/Modbus - Claus for Concern

## Challenge Overview
This challenge simulated a compromised industrial control system responsible for TBFC’s drone-based gift delivery.
Although the system appeared operational, the underlying PLC logic had been manipulated, causing drones to deliver incorrect items.
The objective was not to manually exploit the PLC, but to analyse the provided scripts, understand the malicious configuration, and safely restore the system to retrieve the flag.

## Learnings
From this challenge, I learned:
- How SCADA/ICS challenges can be solved through script analysis rather than direct exploitation
- The importance of reconnaissance scripts in identifying system state
- How restoration scripts safely revert malicious configurations
- Why blindly modifying PLC values can be dangerous
- How challenge authors embed logic and safety checks into remediation tools

## My Solve 
I first reviewed the provided `reconnaissance.py` script to understand the current state of the system. The script connects to the Modbus service and reads key holding registers and coils to determine what configuration is active. 
By running this script, it became clear that the system had been altered to deliver incorrect payloads and that protective logic was in place to prevent unsafe changes.

Instead of manually modifying any PLC values, I followed the intended remediation path by executing the `restore_christmas.py` script.
This script safely resets the malicious configuration by restoring the correct delivery settings and re-enabling required safeguards in the proper order. 
Once the system state was successfully restored, the script output displayed the flag, confirming that Christmas deliveries had been recovered.

## Flag / Answers
```
THM{eGgMas0V3r}
```
