# IDOR - Santa’s Little IDOR

## Challenge Overview
This challenge focused on understanding and exploiting IDOR (Insecure Direct Object Reference) vulnerabilities in a web application.
The objective was to identify how improper authorization checks allow users to access or manipulate data belonging to other users by modifying object references such as IDs, encoded values, hashes, or predictable identifiers.
Through a series of increasingly disguised examples, the challenge demonstrated how IDOR vulnerabilities can appear in real-world applications and how attackers can exploit them to perform horizontal privilege escalation.

## Learnings
From this challenge, I learned:
- What **IDOR (Insecure Direct Object Reference)** actually is at a practical level.
- The difference between authentication (who you are) and authorization (what you’re allowed to access)
- How IDOR commonly results in horizontal privilege escalation
- How browser **Developer Tools** (**Network** & **Storage tabs**) are critical for identifying IDOR
- How predictable or deterministic identifiers can still be brute-forced or reproduced

## My Solve 
After logging in with the provided credentials, I inspected the application using the browser’s Developer Tools.
By monitoring the Network tab, I identified a request named:
`view_accountinfo`
This request included a parameter called `user_id`, which was set to `10`. The response clearly returned account information belonging to the logged-in user,
indicating that the backend relied directly on this value to fetch user data.

**Exploiting the IDOR**
I navigated to the Storage → Local Storage section in Developer Tools and located the `auth_user` object. Inside it, the `user_id` value was stored client-side.

By manually changing: `user_id` = `10` → `user_id` = `11` and refreshing the page, the application loaded another user’s data without any access control checks.
This confirmed a classic IDOR vulnerability.
To answer the final question, I iterated through different `user_id` values in the same manner and observed the responses returned by the view_accounts endpoint.
By changing the user_id parameter and refreshing the page, I identified that:
`user_id` = 15 corresponded to a parent account with 10 children :
<img width="1920" height="1070" alt="Screenshot 2025-12-21 004545" src="https://github.com/user-attachments/assets/a3db6701-f8ee-42ef-be34-644c758cd917" />

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
