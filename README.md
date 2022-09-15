The final room in the Web hacking series in the complete beginnner learning path is a vtf to find 3 ingredients to fix PICKLE RICK!

Naturally the first thing to do is a basic nmap scan
	2 open ports
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Looking at the code on the web page we can see a comment that says to remember his username:
	R1ckRul3s

ssh connection permission denied because of lacking public key.

GoBuster Scan gives us 

/assets               (Status: 301) [Size: 315] [--> http://10.10.196.203/assets/]
/.htpasswd            (Status: 403) [Size: 297]                                   
/.htaccess            (Status: 403) [Size: 297]                                   
/index.html           (Status: 200) [Size: 1062]                                  
/robots.txt           (Status: 200) [Size: 17] 
/server-status        (Status: 403) [Size: 301] 

Apart from Rick's phrase in the .txt file there is nothing of use here. So I am running a Nikto scan

Definitely a possible exploit in Apache/2.4.18 as its outdated

+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.

Eventually the Nikto Scan presented me with a login.php page that the other software couldn't find.

log in user password from the robots.txt file and username from comment in code 
R1ckRul3s:Wubbalubbadubdub

After being presented with the Rick portal we can execute ls

Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt

The ingredient is found by using strings on the .txt file or viewing it through firefox
	tried cat first but wasn't allowed

The clue says Look around the file system for the other ingredient.

whoami command shows that I am www-data and trying to access any of the other pages denies me permission and says that I need to be the real rick to access these pages

ls /home shows that there is a user called rick and a user called ubuntu.

When I navigate through /rick file system I find the second ingredient file
and running 
	strings /home/rick/"second ingredients" returns to me the ingredient.

With no clues for the third ingredient and access restricted to to REAL RICK it is reasonable to assume that privledge escalation is the route to the third ingredient
	One could say I have to find the Route to Root


sudo -l returns that any user can run a command with a password

Matching Defaults entries for www-data on ip-10-10-196-203.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-196-203.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL

and running sudo ls /root/ shows me
3rd.txt
snap

This means I can simply run sudo strings /root/3rd.txt and be presented with the 3rd ingredient.

In Review

There were paths that I thought may be useful at the beginning of this CTF such as finding an exploit for the Apache version. Fortunately I was able to move forward because I used a variety of ennumeration tools.
As this is the first CTF I have properly been able to complete I am very pleased with how it worked out and was a ton of fun. I look forward to more of these in the near future.
