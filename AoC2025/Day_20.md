# Race Conditions - Toy to The World

## Challenge Overview
This challenge focused on identifying and exploiting a race condition vulnerability in a web-based shopping cart application.
The application allowed users to purchase limited-stock items, but failed to properly handle simultaneous checkout requests. By abusing this flaw, it was possible 
to process multiple purchases at the same time, causing the item stock to drop below zero and reveal hidden flags.

## Learnings
From this challenge, I learned:
- What a race condition looks like in a real web application
- How improper synchronization during checkout can cause overselling
- How Burp Suite Repeater can be used to send parallel requests
- Why atomic transactions and concurrency controls are critical
- How real-world attacks exploit timing rather than complex payloads

## My Solve 
After logging into the application using the provided credentials, I made a normal purchase of the `SleighToy Limited Edition` item. Using `Burp Suite’s Proxy → HTTP history`, 
I captured the `POST` request sent to the `/process_checkout` endpoint during checkout. This request was then sent to `Burp Repeater`, duplicated multiple times, and grouped together. 
I used `Send group in parallel (last-byte sync)` to fire all checkout requests simultaneously. Because the application did not lock or recheck stock properly, 
every request succeeded before the inventory updated, pushing the stock into negative values. Once the stock went negative, the application displayed the flag. 
The same steps were repeated for `Bunny Plush (Blue)`, resulting in a second flag when its stock dropped below zero.

## Tools Used 
- Burp Suite

## Flag / Answers
- SleighToy Limited Edition (negative stock)
```
THM{WINNER_OF_R@CE007}
```
- Bunny Plush (Blue) (negative stock)
```
THM{WINNER_OF_Bunny_R@ce}
```
