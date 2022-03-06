---
layout: post
title:  "Newsdock: What and Why"
excerpt: "A writeup on Newsdock and the problems it solves for me."
date: 2022-03-03 02:15:20 -0500
---

&nbsp;&nbsp;&nbsp; Back in 2021 I was eagerly awaiting the final episodes of [Arcane](https://www.imdb.com/title/tt11126994/) to finish airing on Netflix.
 On November 20th the last 3 episodes came out and I had plans that night to watch it that night with a few friends.
 Unfortunately I made the mistake of using YouTube that morning and in my feed I was greeted with a thumbnail that spoiler for a major event in this week's episodes.

&nbsp;&nbsp;&nbsp; I was pissed.

&nbsp;&nbsp;&nbsp; It was insane to me a show that had been out for less than 10 hours was already being spoiled by YouTube's feed algorithm.
 So I took an oath to stop using YouTube and use a custom content aggregator in the form of [Newsboat](https://newsboat.org/), an RSS reader.
 This did help me avoid scrolling through YouTube and seeing videos I didn't want to see, but I thought I could take this a step further.
 If I could keep all the site's content that I cared about offline for my RSS feed I wouldn't have to visit the site at all when opening the link on Newsboat.
 Thus [Newsdock](https://github.com/Ragnyll/newsdock) was born.

|![newsdock logo](/assets/2022-03-05-newsdock-alpha/newsdock_logo.png)|
|:--:|

## What is [Newsdock](https://github.com/Ragnyll/newsdock)?
&nbsp;&nbsp;&nbsp; Newsdock is a mechanism I wrote for caching and opening Newsboat feed items locally.
 If the content has been cached by Newsdock and exists on your computer it will be opened with your OS's default programs, otherwise it will take you to the site it is sourced from.

![demo](/assets/2022-03-05-newsdock-alpha/demo.gif)

&nbsp;&nbsp;&nbsp; So I'll kick off `newsdock update` when I start my computer (through my systems startup scripts), then when I try to open feeds from Newsboat that include videos I can just watch it on my preferred video player, [MPV](https://mpv.io/), as opposed to the browser defined by Newsboat.
 If the feed item is a textual document I can read it using a version of the webpage cached by Newsdock.
 It's a pretty simple tool, but I am pretty happy with it.

&nbsp;&nbsp;&nbsp; For more on how to configure and use it, check out the project's [README](https://github.com/Ragnyll/newsdock#readme).

## Why Newsdock?
&nbsp;&nbsp;&nbsp; In recent years I've been getting more frustrated with the way platforms encourage us to consume massive amounts of low effort content.
 A lot of video media is just someone clipping a longer movie or show into a 30s clip, cropping it, then and then uploading 50 of these a day
 Platforms like YouTube and Instagram and Facebook promote this by shoving as much of it in front of an audience as they can on the home or discovery pages.
 It's easy to fall down a hole of just mindlessly scrolling and watching bottom of the barrel videos and memes because it's just always in front of us.
 That's not to say social media is bad, but the way it is curated, I believe, is predatory.

&nbsp;&nbsp;&nbsp; With RSS readers like Newsboat we can curate our feeds a little better so as not to fall into ruts of constant consumption.
 I just wanted to go further with this idea with Newsdock.
 By keeping certain media on my device I wouldn't even have to tempt myself on the internet.

## Where does Newsdock go from here?
&nbsp;&nbsp;&nbsp; Newsdock has hit it's [`0.1-alpha2`](https://github.com/Ragnyll/newsdock/releases/tag/v0.1-alpha2) release.
 Anyone who wants to use it should have a minimally working experience.
 There are a handful of issues left to improve the project's efficiency (such as correctly implementing [async code](https://github.com/Ragnyll/newsdock/issues/7)), features (like supporting a [Newsdock config file](https://github.com/Ragnyll/newsdock/issues/19)), and code quality ([like writing tests](https://github.com/Ragnyll/newsdock/issues/47)).
 I'll tackle these as I feel like it, but if this project get's any real traction I'll def put more focus on them.
 Of course I will review any pull requests that come my way.
 For the near term though, I'm satisfied letting this sit in an alpha release since it does what I need it to.
 In the more distant future I would like to move to a beta and a real first release.

_The below are my personal feeling following the project.
 So if you were just here to figure out what Newsdock is then feel no need to read further._

## What I've learned from Newsdock:
 Newsdock is the second major project I've written in Rust.
 [R-Todo](https://github.com/Ragnyll/rtodo) was my first and it was deeply flawed.
 I had picked up R-Todo as a project to really learn the language instead of just writing tiny single file tools, so when I was developing it I made a huge number of bad assumptions, and just sort of designed it as I was coding it.
 I tried to improve on my development patterns in Newsdock so I came in with a design.
 Having read a few more books on Rust I feel that I was in a much better place to make this decent.
 However, I'm not as seasoned as I would like to be in Rust, and so I did my best with what I thought was a sensible design or pattern.
 I for sure made a lot of mistakes that a stronger engineer would cringe at, but this was a marked improvement from R-Todo.
 I'll just make sure to keep educating myself so I can write better and better software as I continue down this career path.

&nbsp;&nbsp;&nbsp; I'll be honest, this project took a lot out of me.
 I've been working on it since the end of December on top of my full time job, a side job, and preparing for interviews, so my mental bandwidth over the last couple of months has been pushed to it's limit.
 I thought this project would be easy and wouldn't take as long as it did, but other obligations slowed it down, and I did not think I would hit nearly as many corner cases as I did on this project.
 Thankfully, Rust does a good job of forcing me to handle those oddball cases sooner, otherwise I would have had to spend significantly more time debugging, testing, and coding.
 I honestly don't know how much longer this would have taken had I chosen to develop this in Python or something else.
 In any case, this whole project has been a good learning experience so hopefully the next thing I do can move a little quicker.
