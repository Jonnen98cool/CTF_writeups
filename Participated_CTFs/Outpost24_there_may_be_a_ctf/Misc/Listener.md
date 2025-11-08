# Listener (300p)  
## Challenge Description  

<em> In some of the challenges you might need access to a listener account. If you solve this challenge you get both a flag and you can use the account as a listener throughout the CTF. Your mission is to log in to the server using the provided passphrase-protected SSH key, retrieve the flag and access the listener account. The flag will be displayed upon successful login.

Important Rules:

    This listener account is a shared resource for all participants.
    Be careful and respectful. Do not destroy or tamper with others' work.
    Clean up after yourself when you're done using the listener account.
    Do not attempt to gain root access.
    This is not part of the challenge and violates the spirit of fair play.
    Any violation of these rules will result in immediate disqualification and your name will be added to the wall of shame permanently.

Author: Mikael Svall  
`ssh -i id_rsa listener@listener.appsec.nu`   
File: [id_rsa](helper/id.rsa)</em>

## Thought Process
1. "Ah, a passphrase protected `id_rsa`, I am no stranger to this. Since the challenge is only 300 points it is probably crackable with rockyou.txt."
2. Convert file into format which `john` the ripper can crack: `ssh2john id_rsa > id_rsa_john`
3. Brute-force file with rockyou.txt: `sudo john --wordlist=rockyou.txt id_rsa_john`
4. \*After 2 hours of bruteforcing with rockyou and no results\*: "Hmm, this can't be it. Let's try other smaller dictionaries"
5. \*Trying several other dicts, including top 10000 passwords and top 10000 enlgish/swedish words\*
6. \*Researching how `hashcat` can be used to crack SSH keys in order to utilize the more powerful GPU, seems to be no easy way\*
7. "There must be something we have missed, one team solved it within 1 hour"
8. \*Can't figure it out, move on to other challenges\*

## Original Solution
1. Less is more. The solution was hidden in plain sight, as a glance upon the private key reveals the following message: `the password+is+O24CTFrocks`
2. Change to acceptable permissions `chmod 600 id_rsa`
3. Attempting to SSH into the machine gives me `Load key "id_rsa": error in libcrypto"` Upon googling this error, I found that it is commonly caused by a missing newline at the end of the file. I opened the file, pressed ENTER at the end, and saved, but still the same error. I then ran `dos2unix id_rsa` (suggested in a comment on Stack Overflow), and finally the error was gone.
4. `ssh -i id_rsa listener@listener.appsec.nu`, enter the passphrase `O24CTFrocks`, and we get the flag.

## Updated Solution
1. Challenge was updated to no longer require private key + passphrase authentication. As the updated description suggests, you use `ssh listener.appsec.nu` to connect. When prompted for password you enter the password provided in plaintext in `id_rsa`.
