# Shortify (500p)
## Challenge Description  

<em> Some careless developer left a testing environment for a demo app exposed the public internet, see what you can find.
https://shortifier.appsec.nu </em>

## Thought Process
- When SSHing in to the machine with the private key did not work (challenge was bugged), I tried looking for other important files on the server. For example a `flag.txt`, `devuser`'s `.bash_history` for clues, and server log files for a log poisoning attack.

## Solution
1. There is some interesting functionality on the website you can test, but that is not the solution here. The key thing to notice is the `https://github.com/aio-libs` at the bottom, which suggest the framework used to power the website. This combined with the Discord hint: `... look at the stuff running the service more than the service itself` means it probably has something to do with the framework.
2. When testing a webapp and seing some technology used - especially if you have the version number of that technology - you should google something like `*technology_here* exploit`. In this case we have a version number sent in the http response header `server: Python/3.11 aiohttp/3.8.6`. Upon googling "aiohttp exploit" we are made aware of `CVE-2024-23334`, a Directory Traversal vulnerability which affects versions < 3.9.2. Bingo!
3. At this point you could search for Proof of Concept (PoC) code associated with the appropriate CVE which can perform the exploit for you. This can be very convenient when the exploit is tricky. In this case it was pretty simple, as it only required sending a GET to `/static/` for the directory traversal to work.
4. Now to perform the traversal. We first want to confirm it works by navigating to a known file. For a linux server there are usually numerous known files to go to. A good one is `/etc/passwd` as it usually has read permissions for all users. When entering `https://shortifier.appsec.nu/static/../etc/passwd` in the URL we notice that it's not actually what's being sent, only `https://shortifier.appsec.nu/etc/passwd` is sent which is not what we want. We intercept and manually edit the GET request in Burp, first line should look like this: `GET /static/../etc/passwd HTTP/2`. This gives us 404, so we try one directory up: `GET /static/../../etc/passwd HTTP/2`and bingo bongo bango, we are served `/etc/passwd`.
5. With the exploit confirmed working, it is now time to think how we could use it. The server's `/etc/passwd` tells us that there are two users with access to a shell: `root` and `devuser`. We navigate to `/home/devuser` and get response 403 instead of 404, confirming it exists. We then try `/home/devuser/.ssh/id_rsa` and we are able to acquire `devuser`'s SSH private key, meaning we can probably log in to the server with SSH. I use `sudo nmap -sV -p- shortifier.appsec.nu` to confirm that SSH is up on port 22. (This challenge was bugged and you couldn't SSH in initially. I found it **very** odd that we could find the user's private key but not use it to log in, so I contacted the dev and they fixed it.)
6. With us now supposedly being able to log in with SSH, I try again: `ssh -i id_rsa devuser@shortifier.appsec.nu`. It didn't work this time either, so I re-scanned the network and found that port 2222 was now open and the service associated was OpenSSH. I do `ssh -i id_rsa devuser@shortifier.appsec.nu -p 2222` and get in. The flag found with `cat user.txt`.