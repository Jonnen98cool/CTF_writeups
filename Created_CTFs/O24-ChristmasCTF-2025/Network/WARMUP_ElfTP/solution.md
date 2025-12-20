1. Do an NMAP scan to uncover services running on the host: `sudo nmap -sV *ip*`
2. There's an FTP server running on port 21. Try to connect to it, using the common `anonymous` as username and a blank password: `ftp *ip*`
3. Once in, show everything in the current directory: `ls -la`
4. There's a `flag.txt` file. Download it with `mget flag.txt`
5. Find the downloaded file and read it: `H0_H0_H0_R3m3mb3r_T0_U53_Pr0p3r_Cr3d3nt14l5_F0r_S3rv1c35`
