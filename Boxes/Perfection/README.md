Perfection
We start off by running nmap tcp scan. We detected an HTTP Server. 

Running nikto does not return any useful information. Also bruteforcing directories, I did not find any hidden accessible resource to dig.




Going through website and tampering with functionality, we have a average grade calculator. Investigating network tab in the browsers developer tools, shows the data is been sent to server via a POST request. To make the task of tampering with parameters easier, I wrote down a simple python script using "requests" library in order to automate the task of sending requests to the server. I tampered with the parameters a little, but I keep getting blocked, so I did a little research. One of the methods of bypassing WAFs (Web Application Firewall) and other systems of filtering is through inserting "\0" (null character) in the response. Unfortunately though the "requests" library performs encoding on the parameters so I need to deviate from APIs standard usage in order to sent the post parameters (grade calculating data) in raw format. The application is vulnerable to this method of bypassing. Now the first thing I attempt was to perform XSS and application is vulnerable. But we need a server-side vulnerability to compromise the server. After some testing I realized server is vulnerable to template injection (SSTI). The back-end language is ruby, therefore we use ruby payload. We can proof vulnerability using arithmetic phrases, now I injected reverse-shell generated from revshells.com and successfully got a shell back.

Now we have pawned system at the user level. In order to get better shell, and in case of loosing connection, get a reverse-shell easier, I config my ssh access by generating a pair of public/private key and adding the public key to the user's authorized users.

In order to escalate privilege, I uploaded and ran linbean tool on the server, while manually checking suid privileges and exploring user's home directory. There is a sqlite database including password hashes. Based on the lenth, they're seems like sha-256 hashes which are used on linux accounts. The linpeas output shows we have sudo privileges since user account we pawned is in sudo group. I ran hashcat on this hashes against "rockyou" and a couple of other well-known dictionaries but could not crack it. After a through investigation of the systems, I found an email to our user instructing to change account password and explaining the password format, most of the password format is fixed but it leaves room for a milion space. I wrote another python script for cracking the password. And thus I were able to pwn server at the rool level.
