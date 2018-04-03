---
layout: post
title:  "pass For Everything"
excerpt: "How to manage your passwords without losing your mind."
date:   2018-04-01 17:15:20 -0500
---

## The Problem ##

&nbsp;&nbsp;&nbsp;&nbsp;Password management is a pain. If you're keeping all your passwords in your head, chances are you have to keep 10-12 stuffed up there at a given time and that's if you're not recycling passwords across multiple services (if you are, shame on you).
 Keeping many more than that in your head is an absolute nightmare.

&nbsp;&nbsp;&nbsp;&nbsp;Imagine this, tomorrow morning you wake up and get an email from your bank.
 They've had a data breach and all the sudden all your user credentials are no longer safe, and they urge you to change your password.
 Great, now that password that you've spent the effort to memorize is no longer useful.
 If you've used it anywhere else (again, shame on you if you have, especially a bank password) then you need to recycle those too.
 Later that day you go to log in to your Netflix account.
 "Wait, which password was that that again?" You ask yourself, and you begin mentally shuffling through every password you can remember and still come up blank.
 You go reset your password after about 5 minutes of wasted time and then have to add another password to that leaky bucket you call a brain.

## An Answer ##

&nbsp;&nbsp;&nbsp;&nbsp;Thankfully, we have password managers.
 You throw all your passwords into a computerized vault, then put in your new combination to unlock it.
 This works pretty well, assuming that the vault is safe that is.
 There's all kinds of software out there to do this for us.
 You can use `keepass`, `LastPass`, `Intel True Key`, or my weapon of choice, [`pass`](https://www.passwordstore.org/).

&nbsp;&nbsp;&nbsp;&nbsp;This article is not a comparison of all these different password managers.
 Honestly, me writing out a pros cons list is beating a long dead horse.
 This is also not an explanation of public key cryptography.
 This is a short guide on how to get running with pass.
 This I find to be actually useful, because getting this to work (particularly across multiple systems) is tricky.
 There are a lot of places where you can get stuck for easily a few hours if you're new to some of this software.

## A quick praise for pass ##

&nbsp;&nbsp;&nbsp;&nbsp;There are a few praises I would like to give `pass` before I get into this for real.
 I love the unix philosophy of 'a program should do one thing extremely well'.
 At it's core, all pass really does is organize `gpg` encrypted files.
 There are a few other useful functions useful for managing passwords that are nice.
 It has a password generator, a listing function, a copy utility, and a few other things.
 Also, it's primarily a command line tool.
 There are other gui wrappers for it, and that's fine, but I'm a terminal junkie so I tend towards the former.

&nbsp;&nbsp;&nbsp;&nbsp;Anyways, without further ado lets get into it.

## Quick Start ##

&nbsp;&nbsp;&nbsp;&nbsp;Install `pass` and `gpg` (> v2.0).
```sh
$ sudo apt-get install pass
$ sudo apt-get install gpg
```
 &nbsp;&nbsp;&nbsp;&nbsp;Seriously though, check that your version of gpg is greater than 2.0 with a quick `gpg --version`.
 pass depends on gpg2 to encrypt all of the password files, if you try to use anything less than v2 then it just wont work.
 If you find that you need to have both installed then just install gpg2 as well and everything should work out the same, just make sure you use `gpg2` instead of `gpg` when I mention it from here on out.

&nbsp;&nbsp;&nbsp;&nbsp;Now you're going to generate your key pair. For the sake of this example we're going to assume that you don't want the key to expire and you implicitly trust everyone and all the devices that you're going to give this key to.
 Generate the key with:
```sh
$ gpg --full-gen-key
```
&nbsp;&nbsp;&nbsp;&nbsp;The prompts walk you through the whole process but for this I suggest following:
```
RSA and RSA
4096
0 (Key does not expire)
```
&nbsp;&nbsp;&nbsp;&nbsp;The rest is pretty self explanatory. You can go with the 2048 bit key if you want and _probably_ not worry to much about it.

 Next you're going to intialize the password store.
 Grab the public key from gpg:
```sh
$ gpg --list-keys
 /home/dogman/.gnupg/pubring.kbx
--------------------------------
pub   rsa2048/62B478RT 2018-01-22 [SC]
uid         [ultimate] Dogman <dogman@woofman.com>
sub   rsa2048/5TH7N678 2018-01-22 [E]
```
and pass that as the public key argument to pass

 ```sh
 $ pass init 62B478RT
 ```

&nbsp;&nbsp;&nbsp;&nbsp;Congratulations, you can now run pass on one machine!
 You can add existing passwords with `pass insert service1/user1` and generate random ones with `pass generate service2/user1` and it will walk you through it.
 Yank the password into your clipboard with `pass -c service1/user1` and you're good to go.

#### A Small Concern ####
&nbsp;&nbsp;&nbsp;&nbsp;There is one fairly common concern that I think is best addressed now rather than later, some consider pass to be "leaky" when it comes to user and service metadata.

&nbsp;&nbsp;&nbsp;&nbsp;That's not completely true.

&nbsp;&nbsp;&nbsp;&nbsp;If you do something like the above example I showed you (with `pass insert service1/user1`) if somehow someone gets a hold of your raw store they now know you have an account (user1) at a specific service (service1).
 All these passwords are gpg encrypted files, they can be named whatever you want, but this particular approach is considered slightly problematic.
 You may consider nicknaming them, or naming them generically, it's really up to you.
 Though this may be a secondary concern.
 The files are only useful if they are decrypted.
 Decryption is only possible with your private key, which should be password protected as well.
 This isn't the most likely point of failure, but it's more information than a hacker may have had before.
 Long story short, guard your private key, and employ common sense.

## Managing pass On Multiple Devices ##
&nbsp;&nbsp;&nbsp;&nbsp;You can use pass all over the place, so long as you can decrypt the files.
 For the sake of this example I'm just going to use a one computer, one Android phone setup, but you can scale this pretty easily across however many devices you have.
 One more thing though, this is what I'm going to call "the dirty way" of doing this.
 The safest way is probably to have a gpg key for each device, then to trust each of these keys.
 That, or use some sort of hardware key, like yubikey for decrpyting these on you different devices.
 I may get into those topics another time, but this is the quick and dirty of how to get pass going.

### Managing Your Store With git ###

&nbsp;&nbsp;&nbsp;&nbsp;I think that this goes without saying, but DO NOT use a public git repo.
 Seriously, if you're going through all this this trouble to secure your passwords don't do the obviously stupid thing of storing them in a completely public place (like a public Github repository).
 At the very least store them in a private repo, where not everyone can get to them.
 Use a private repo on Github (if you wanna pay for that), or a private Bibucket repo (you don't have to pay for that), an AWS hosted repo, or a Gitlab repo you have hosted at home.
 There are drawbacks to each of those approaches, and I'll leave it up to you to decide which you personally believe is your best option, but do your best to hide this away from the rest of the world.

&nbsp;&nbsp;&nbsp;&nbsp;I suggest creating the the repo from whatever online client first, if you can, then clone that and start the password store management business.
 The reason for this is the android app were going to be using only supports bare repositories.
 You can do that from the command line, but that is slightly more obnoxious to get figured versus creating the bare repo and cloning that down. So go create your repo then clone it down to local.

&nbsp;&nbsp;&nbsp;&nbsp;`pass` actually has an option to manage it with git upon it's initialization with `pass git init`.
 I actually dont suggest using that.
 The local repository it creates is not a bare repo, which will lead to a little more pain later.
 Theres that, and I also suggest another strategy for versioning than what may be your first reaction.
 Now we're going to be symlinking the password files in our `~/.password-store/` (the default) into the cloned repo.
 The reason I like to this vs the `pass git init` way is that it makes it easier to explicitly say what passwords you want to keep out on remote.
 `pass git init` sets a gitattribute (which now that I'm writing this I guess you could just remove) that automatically adds and commits every password you insert.
 With the symlink way of doing this you can keep a set of passwords from every being added to remote and be perfectly fine.
 So `cd` over into your cloned password repo and begin writing out your symlinks.
```
$ cd my_passwords_repo
$ mv ~/.password-store/pass1 .
$ ln -s ~/.password-store/pass1
```
&nbsp;&nbsp;&nbsp;&nbsp;Now push your repo on up and we're going to be moving onto the mobile side of things.

### The mobile side of things ###

&nbsp;&nbsp;&nbsp;&nbsp;You're going to need a few things.
 First, a remote git repo in which to store your passwords (Check).
 Second, two new android apps: a key manager, and a password manager compatible with pass.
I suggest [OpenKeychain](https://www.openkeychain.org/about/) and [Password Store](https://github.com/zeapo/Android-Password-Store).
 OpenKeychain just manages keysets, and Password Store is an android wrapper for pass.

&nbsp;&nbsp;&nbsp;&nbsp;Once you get those apps open Password Store first, the open up settings and make sure external repository is disabled.

&nbsp;&nbsp;&nbsp;&nbsp;If you're not already you should interacting with remote via SSH, especially for this part.
 Generate an SSH key pair from the app using the `Generate SSH key pair` and go associate it with your remote git service.

&nbsp;&nbsp;&nbsp;&nbsp;Back in settings select Edit git server settings.

&nbsp;&nbsp;&nbsp;&nbsp;Use `ssh://` as your Protocol and `ssh-key` as your Authentication Mode.
 Really, I'd just ignore the stuff under `Server` and just directly type the `Resulting URL`.
 It's way more straightforward, especially if you have the clone button on your remote server right there with the URL in front of you. Now just go back to the password store main page and pull from remote.

&nbsp;&nbsp;&nbsp;&nbsp;Great now you have all your passwords in your Password Store app. You can open them and look at them yet though, because they are encrypted, which is good!
 That means everything is working as expected.

&nbsp;&nbsp;&nbsp;&nbsp;Now for the dicey part, getting your secret key over to your android device.

&nbsp;&nbsp;&nbsp;&nbsp;Back at your computer on the terminal your going to need to export your key. Run:
```sh
$ gpg --armor --export-secret-keys dogman@woofman.com | gpg --armour --symmetric --output mykey.sec.asc
```
 This will put your key in a format in which it can be transferred around.
 Quick tip though:

 FOR THE LOVE OF OLD PAPA LINUS DO NOT SEND YOUR KEY TO YOURSELF IN AN UNENCRYTPED EMAIL!

&nbsp;&nbsp;&nbsp;&nbsp;Once again, your private key is the one thing you do not want compromised.
Don't be stupid and do something so obviously insecure.

&nbsp;&nbsp;&nbsp;&nbsp;I suggest directly transferring the key (mykey.sec.asc) to your mobile via usb.
 This way, you know that it's getting from point a to b.
 Once your done delete that mykey.sec.asc file (don't worry your secret key is still there for you to use on your machine).

&nbsp;&nbsp;&nbsp;&nbsp;Now over in your OpenKeychain app select `Manage my keys` and `Import Key From File`.
 Find where you stored your key and add it on in.

&nbsp;&nbsp;&nbsp;&nbsp;You're almost done now!
 Go back to the Password Store app and scroll down to Crypto.

&nbsp;&nbsp;&nbsp;&nbsp;Select `OpenKeychain` in `Select OpenPGP Provider`.

&nbsp;&nbsp;&nbsp;&nbsp;Next select your key in `Select OpenPGP Key id`.

&nbsp;&nbsp;&nbsp;&nbsp;Finally you can start decrypting your password on your mobile and use them.
 Go back to the main Password Store menu and pick a password.
 It will be live for 45 seconds for you to look at or copy over into another application.

## Conclusion ##

&nbsp;&nbsp;&nbsp;&nbsp;`pass` is an amazing and simple password manager for the linux minded people out there.
It's super flexible, and is a great way to get away from insecure password reuse and memorization.

### References ###
pass: [https://www.passwordstore.org/](https://www.passwordstore.org/)

OpenKeychain: [https://www.openkeychain.org/about/](https://www.openkeychain.org/about/)

PasswordStore:[https://github.com/zeapo/Android-Password-Store](https://github.com/zeapo/Android-Password-Store)
