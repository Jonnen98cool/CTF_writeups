# Ping (User Flag) (300p)
## Challenge Description  

<em> The evil AI Wintermute has taken over our web-based ping tool! This tool was previously vulnerable, but Wintermute has patched the known vulnerabilities, making it more challenging to exploit. Your mission is to find a new way in and save the summer from Wintermute's icy grasp

Author: Mikael Svall  
https://ping.appsec.nu/ </em>


## Solution
1. Looks like user input is used for the `ping` command. This screams OS injection.
2. We try to bypass it with `google.com;ls`. The `;` operator in a linux shell ends the current command and lets us start a new one. This doesn't work, so we try `google.com&ls`. `&` after a command makes that command run in the background. This doesn't work either, so we try the pipe operator, which lets us "pipe" the output of the first command to the input of the second: `google.com|ls`. This works, and we can discard the ping command and merely do `|ls`
3. We try `|ls -la`, but this doesn't work. Hmm, maybe certain input is whitelisted and that was not on the list. We try commands like `|cat *file*` and `|id` and some of them work but some don't. We notice a pattern that whenever there is a space in the command, it doesn't work (although `| ls` works). We can either try to use commands without space or find an alternative for it. Turns out that we can invoke the `${IFS}` variable which translates into a space. Now we can do `|ls${IFS}-la`.
4. I tried to get a reverse shell here but it was not as straightforward as I had hoped. I'm leaving that for Ping (root). Instead I look around the file system and find the flag in `/etc/flag` (`|cat${IFS}/etc/flag`).
