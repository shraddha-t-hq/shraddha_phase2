# Phishing - Merry Clickmas

## Challenge Overview
This challenge focuses on social engineering attacks, specifically phishing, to demonstrate how human factors can be exploited to compromise security.
The objective was to design and deliver a convincing phishing campaign that captures valid user credentials by hosting a fake login portal and distributing it via a realistic phishing email.
The challenge also highlights how stolen credentials are often reused across multiple services, amplifying the impact of a single successful phishing attempt.

## Learnings
This task reinforced how psychological manipulation—such as authority, urgency, and trust—plays a critical role in social engineering attacks. 
I learned how phishing infrastructure is set up end-to-end, including hosting a credential-harvesting login page, delivering malicious links through believable emails, 
and monitoring captured credentials in real time. The challenge also demonstrated the dangers of password reuse and why user awareness training and strong credential hygiene are essential defenses against phishing-based compromises.

## My Solve 
I began by launching the provided phishing server script, which hosted a fake TBFC login page and listened for incoming credentials. 
After verifying the page locally in the browser, I used the Social-Engineer Toolkit (SET) to craft and send a convincing phishing email impersonating a trusted shipping partner, embedding a link to the malicious login portal.
Once the target interacted with the email and entered their credentials, the server captured and displayed the login details in the terminal.
Using the harvested password, I then accessed the factory user’s email portal to confirm credential reuse and successfully retrieved sensitive operational information, completing the attack chain.
<br/>
<img width="1920" height="1065" alt="Screenshot 2025-12-17 115228" src="https://github.com/user-attachments/assets/b52f8e5d-41eb-4997-9411-e515ccc5e876" />

## Tools Used 
- SET tool kit

## Flag / Answers
- Password used to access the TBFC portal: **unranked-wisdom-anthem**
- Total number of toys expected for delivery: **1984000**
