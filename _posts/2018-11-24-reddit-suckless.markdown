---
layout: post
title:  "Reddit Suckless"
excerpt: "How I made browsing Reddit suck less."
date:   2018-11-24 00:15:20 -0500
---

&nbsp;&nbsp;&nbsp;&nbsp;I really do not like browsing Reddit.
 I think the search functionality is a pain to use (though they have made some good changes through the redesign), and following specific topics is a huge pain.
 Even if you create a multireddit of only the subreddits you care about following you still have to sift through a large amount of junk. 
 For example, if on r/linux I'm only interested in posts and discussions that relate to kernel updates I still have to go the subreddit and do a search for that topic.
 Really what I want is a multireddit that only includes very specific kinds of posts.
 So why not just create a multireddit then do searches on that?
 Well, then every time I want to look at those topics I have to remember and reenter that search every time.
 In the end I decided to write something to do this searching and filtering for me.

&nbsp;&nbsp;&nbsp;&nbsp;On mobile I use 'RedditIsFun'.
 I think it provides the most information in a single view and is the easiest to search in.
 The interface is minimal and straightforward, and I wrote the RedditReporoter with this in mind as my front end tool.

&nbsp;&nbsp;&nbsp;&nbsp;The [RedditReporter](https://gitlab.com/Ragnyll/RedditReporter) is just a basic script that goes through a subreddit, applies a filter on a title, and saves posts made in the last day.
 I run this script on a cron on my raspberry pi at midnight so the next morning I have bunch of saved posts that I can look at.
 It's not the most well thought out code I've ever written, but I spun it up pretty quickly just so I could have this run for me.

&nbsp;&nbsp;&nbsp;&nbsp;In the future with this I'm going to look at applying multiple filters with logical `and` and logical `or` to make the filters a little smarter.
 I also would like a function to do this with an individual reddtor's posts. 
 So if I really like someone's posts about bspwm or linux mint or whatever I can just watch that redditor's posts. 
 Unfortunately [praw](https://praw.readthedocs.io/en/latest/index.html) makes it difficult to just grab a redditor's posts.
 I'll have to filter out their comments so I don't throw up errors.
 Also, I don't really handle the utc conversion very well.
 I don't convert the post time or my time to match the same timezone (UTC); I just give a day buffer, which seems to be working so far.
 I guess I can't complain too much.

&nbsp;&nbsp;&nbsp;&nbsp;As another sidenote, quite a few of my signed commits on the RedditReporter project (and my other proects on Gitlab) are unverified.
 I recently switched the email associated with my Gitlab account to Protonmail (I'm trying to move away from Gmail, which is another story for another time), and my gpg key is still associated with my Gmail account, so it looks like it's coming from a different user.
 I'll have to get my GPG keys in sync again, which is gonna be a pain.

 Anyways, here's the code again.
 Have fun.

 [RedditReporter](https://gitlab.com/Ragnyll/RedditReporter)
