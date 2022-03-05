---
layout: post
title:  "Newsdock: What and Why"
excerpt: "A writeup on Newsdock and the problems it solves for me."
date: 2022-03-03 02:15:20 -0500
---

&nbsp;&nbsp;&nbsp; Back in 2021 I was eagerly awaiting the final episodes of [Arcane](https://www.imdb.com/title/tt11126994/) to finish airing on Netflix.
 On November 20th the last 3 episodes came out and I had plans that night to watch it with a few friends.
 Unfortunately I made the mistake of using YouTube and in my feed I was greeted with a thumbnail that spoiled a major event.
 I was pissed.
 It was insane to me a show that had been available for less than 10 hours was already being served to me in the form of spoiler.
 So I took an oath to stop using YouTube and use a custom content aggregator in the form of [Newsboat](https://newsboat.org/), the RSS reader.
 This did help me avoid scrolling through YouTube and seeing videos I didn't want to see, but I thought to go a step further.
 If I could keep all the site's content offline for my RSS feed I wouldn't have to visit the site at all when opening the link on Newsboat, and thus [Newsdock](https://github.com/Ragnyll/newsdock) was born.

|![newsdock logo](/assets/2022-03-05-newsdock-alpha/newsdock_logo.png)|
|:--:|

## What is [Newsdock](https://github.com/Ragnyll/newsdock)?
&nbsp;&nbsp;&nbsp; Newsdock is a mechanism I wrote for caching and opening Newsboat feed items locally.
 If the content has been cached by Newsdock and exists on your computer it will be opened with your OS's default programs, otherwise it will take you to the site it is sourced from.

![demo](/assets/2022-03-05-newsdock-alpha/demo.gif)

&nbsp;&nbsp;&nbsp; So I'll kick off `newsdock update` when I start my computer, then when I try to open feeds from newsboat I can just watch it on my my preferred video player, [MPV](https://mpv.io/), as opposed to the browswer defined by newsboat.
It's a pretty simple tool, but I am pretty happy with it.

&nbsp;&nbsp;&nbsp; For more on how to configure and use it, check out the project's [README](https://github.com/Ragnyll/newsdock#readme).

## Why Newsdock?
&nbsp;&nbsp;&nbsp; In recent years I've been getting more and more frustrated with the way platforms encourage us to consume massive amounts of low effort content.
 A lot of video media is just someone clipping a longer movie or show into a 30s clip, cropping it, then uploading 50 of these a day, then platforms like YouTube and Instagram and Facebook promote this by shoving as much of it in front of an audience as they can.
 It's easy to fall down a hole of just mindlessly scrolling and watching bottom of the barrel videos and memes because it's just always in front of us.
 With RSS readers like newsboat we can curate our feeds a little better so as not to fall into these ruts.
 I just wanted to go further with this idea with Newsdock.
 By keeping certain media on my device I wouldn't even have to tempt myself on the internet.

## Where does Newsdock go from here?
&nbsp;&nbsp;&nbsp; Newsdock has hit it's [`0.1-alpha2`](https://github.com/Ragnyll/newsdock/releases/tag/v0.1-alpha2) release. So anyone who wants to use it should have a minimally working experience.
 There are a handful of issues left to improve this project's efficiency (such as correctly implementing [async code](https://github.com/Ragnyll/newsdock/issues/7)), features (like supporting a [newsdock config file](https://github.com/Ragnyll/newsdock/issues/19)), and code quality ([like writing tests](https://github.com/Ragnyll/newsdock/issues/47)).
 I'll tackle these as I feel like it, but if this project get's any real traction I'll def put more focus on them.
 Of course I will review any pull requests that come my way.
 For the moment though, I'm satisfied letting this sit in an alpha release.

&nbsp;&nbsp;&nbsp; I would like to eventually move to a beta or real first release, but I'd like to see how it lives in its current state for a while.

_The below are my personal feeling following the project.
 So if you were just here to figure out what Newsdock is then feel no need to read further._

## What I've learned from Newsdock:
 Newsdock is the second major project I've written in Rust.
 [R-Todo](https://github.com/Ragnyll/rtodo) was my first and it was deeply flawed.
 I had picked up R-Todo as a project to really learn the language instead of just writing tiny single file tools, so when I was developing it I made a huge number of bad assumptions, and just sort of designed it as I was coding it.
 I tried to improve on my development patterns for this, so I came in with a design and having read a few more books on Rust I was in a much better place to start.
 However, I'm not as seasoned as I would like to be in Rust, and so I'm doing my best with what I think is a sensible design or pattern.
 I've for sure made a lot of mistakes that a stronger engineer would cringe at, but I'm doing my best to keep looking at new code so I can eventually get better with Rust's programming paradigms.

&nbsp;&nbsp;&nbsp; I'll be honest, this project took a lot out of me.
 I've been working on it since the end of December on top of my full time job, a side job, and preparing for interviews, so my mental bandwidth over the last couple of months has been pushed to it's limit.
 I thought this project would be easy and wouldn't take as long as it did, but other obligations slowed it down, and I did not think I would hit nearly as many corner cases as I did on this project.
 Thankfully, Rust does a good job of forcing me to hit those oddball cases sooner, otherwise I would have had significantly more testing to do and it probably would have taken longer to get out.
 This whole thing has been a good learning experience so hopefully the next thing I do can move a little quicker.

&nbsp;&nbsp;&nbsp; I've already answered where Newsdock goes from here, but where do I go?
 Well I am still job searching, and that's been way more difficult than I originally thought it would be.
 I like to think I'm a pretty decent developer, but the skills I personally value are not necessarily going to make me succeed in interviews.
 There's a lot of CS fundamentals and technologies companies expect proficiency in that tooling projects like this (while fun) don't always build.
 I would love to get into Rust and lower level development professionally, but I have to work at those things to.
 To that end, I'm taking an extended break from job hunting to really polish those skills.
 Hopefully I can make some more random tools for my system along the way that I can share on this blog.
