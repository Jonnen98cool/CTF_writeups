# Shortify2 (500p)
## Challenge Description  

<em> He even exposed the root account, poor guy.  
https://shortifier.appsec.nu </em>


## Lame, Unintended Solution
1. First thing I do when trying to privesc: `sudo -l`. Looks like we can run `/app/tests/debug.py` as root, let's check it out.
2. "Alright, looks to be a short python script. `import sys` that's interesting, could maybe make my own `sys.py` file. Let's see what else... wait"
3. `devuser` has (had\*, challenge-maker updated) write permission on `/app/tests/debug.py`. This means we can just make it spawn a shell. There seems to be no convenient text editor available, so `echo` will have to do. We edit `debug.py`'s code with `echo "import pty; pty.spawn('/bin/sh')" > /app/tests/debug.py`. Calling `sudo /usr/bin/python3 /app/tests/debug.py` now spawns a root shell.

## Intended Solution
1. With the challenge updated, the above technique no longer works. Instead, we inspect the `debug.py` code and realise that the contents of a file `\x2E\x65\x6E\x76` (hex-encoded `.env`) gets passed to `eval()`.
2. Since no absolute path is specified, `open()` will look for a file in the caller's current directory with the name `.env`. We move to a writable directory (e.g. `/home/devuser` or `/tmp`) and `touch .env`.
3. `read()` gets called on the contents of the `.env` file, and this is then passed to `eval()`. This means we control what gets sent to `eval()`. I google how to spawn a shell with `eval()` and find this: `echo "__import__('os').system('/bin/bash')" > .env`. (`exec()` is primarily used for interpreting commands, but `eval()` can do the trick as well.)  
4. `sudo /usr/bin/python3 /app/tests/debug.py` gives us root, flag is in `/root/root.txt`.
