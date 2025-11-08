# Opportunity (500p)
## Challenge Description  

<em> Welcome to our career site! Our web application lists four exciting career opportunities, but unfortunately, the application period for these roles closed yesterday. However, there is a fifth opportunity for a coveted position: the Flag Master, but Wintermute, the malevolent AI adversary, has hacked the web app and hidden this job from plain sight.

Your mission, should you choose to accept it, is to navigate through the web application and find the hidden flag and you get the job.

Author: Mikael Svall  
https://opportunity.appsec.nu/ </em>

## Solution
1. After poking around a bit, i find three references to "glob":
	- "Start your Globbal career here!"
	- "Shake that glob!"
	- "snowglob://"    
That can't be a coincidence, so I google it.
2. Turns out they are references to something called "file globbing", which is essentially using regex-like syntax for filename expansion. I do some research on how it works and try it out by adding the wildcard glob to where `joblist1.txt` normally goes: `https://opportunity.appsec.nu/job_statistics/*`. The functionality which fetches "Size in Bytes of the list" now fetches bytes of all files available, and we see there exists a previously hidden fifth list with 26 bytes. I am definitely on the right track, considering the description.
3. Now it's a matter of trying to read the fifth list which I am confident will contain the flag. You can read the other four lists by going to `/jobs/joblistX.txt`, where `X` is a number 1-4. There is no `joblist5.txt` or `joblist0.txt` and file globbing does not work on `/jobs/`. If we do `/job_statistics/j*`, it selects all files which start with a "j", and the fifth file does not get included. We can use this technique to brute-force-enumerate the name of the hidden file.
4. I use Burp Turbo Intruder to bruteforce, I begin with `%s*` where `%s` is the character to be bruteforced. I use a wordlist of the printable ASCII characters (ascii 32-127) as input. By looking at the response length of the brute-forced requests, i see that `j*` (expected) and `f*` have different lengths than the other requests. Here we go. Next I do `f%s*` to find the second character of the file, and continue this process untill I find the full name. The flag is located at `/jobs/*found_filename*`.
