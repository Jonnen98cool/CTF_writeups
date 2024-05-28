# Find the flag  
## Challenge Description  

<em>The evil AI Wintermute is at it again! This time, Wintermute has hidden a crucial flag within a seemingly innocent image filled with various other flags. It's so clever hidden that Wintermute once said "Who needs strong passwords when you can hide" Your task is to uncover the hidden flag and thwart Wintermute's plans.

You'll need to look beyond the surface to find the flag.

Author: Mikael Svall

File: [stegoflag.jpg](https://github.com/Jonnen98cool/CTF_writeups/edit/main/Outpost24_there_may_be_a_ctf/helper/stegoflag.jpg)</em>


## Solution
1. First thing I always try when suspecting steganography: `steghide --extract -sf stegoflag.jpg`. When prompted for password, I don't enter anything. Didn't work.
2. Maybe there is a file hidden within? Let's try `binwalk -e stegoflag.jpg` to extract any potential files. No luck here either.
3. Perhaps a passphrase is necessary to extract the hidden message, let's try a wordlist brute-force: `stegseek stegoflag.jpg rockyou.txt`. Success! `stegseek` also extracts the file for you, and here we get the flag.
