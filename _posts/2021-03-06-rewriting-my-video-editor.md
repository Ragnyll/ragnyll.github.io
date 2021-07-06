---
layout: post
title:  "Rewriting My Video Editor"
excerpt: "How and why I rewrote my json video editor to not use json."
date: 2021-07-05 02:15:20 -0500
---

&nbsp;&nbsp;&nbsp;&nbsp;_This post was originally meant to be released in March, but I got annoyed with a defect I had to fix in this new implementation.
 I did fix it but I forgot about this post. So here it is now._

&nbsp;&nbsp;&nbsp;&nbsp;Last year I wrote a blog post about my "[breaking-move-editor](https://gitlab.com/Ragnyll/breaking_move_editor)", a project I used to edit my dance footage.
 The idea behind it was that it was more efficient to write a `json` object to represent a video clip rather than open up some beefy editor like [OpenShot](https://www.openshot.org/) (or for windows adobe or some bs like that) and drag clips around until you get it visually correct.
 While I did find that editing videos with json was more efficient than GUI video editors, I still found that that I was making a lot of mistakes when I was editing my videos and the syntax I created for that task was kinda hard to remember.
 The file would look something like this:

```json
[
  {
    "combo_name": "2019_compilation",
    "moves": [
      {
        "from_clip": "/path/to/video.mp4",
        "start_time": "00:02",
        "end_time": "00:30",
        "keep_audio": true
      },
      …
    ]
  }
]
```

&nbsp;&nbsp;&nbsp;&nbsp;I would always copy my previous file, and edit 3 fields over and over (from_clip, start_time, end_time).
 Then I would copy the `move` object over and over until I was done.

&nbsp;&nbsp;&nbsp;&nbsp;Here's what my work space would look like while I was editing it.

![old_workflow](/assets/2021-03-06-rewriting-my-video-editor/old_workflow.png)

&nbsp;&nbsp;&nbsp;&nbsp;I kept 3 applications open at a given time: a video player ([mpv](https://mpv.io/installation/)) , a text editor ([neovim](https://neovim.io/)), and a file browser ([Ranger](https://github.com/ranger/ranger)).
 These things were fine on their own, and I was happy enough editing in this manner, but I got annoyed basically trying to keep up with myself and the common workflow I was doing.

&nbsp;&nbsp;&nbsp;&nbsp;_It was time for a rewrite._

## Trimming The Fat
&nbsp;&nbsp;&nbsp;&nbsp;The first thing that had to go were extra features in the video editor script.
 I had functions for [building notebooks](https://gitlab.com/Ragnyll/breaking_move_editor/-/blob/3b9dccc4c8e8883126237420ebd4aef14d57ac8e/source/move_vid_builder.py#L130) from the json object, I had a function for [filtering names and tags](https://gitlab.com/Ragnyll/breaking_move_editor/-/blob/3b9dccc4c8e8883126237420ebd4aef14d57ac8e/source/move_vid_builder.py#L152), and flags for turning [on and off audio](https://gitlab.com/Ragnyll/breaking_move_editor/-/blob/3b9dccc4c8e8883126237420ebd4aef14d57ac8e/source/move_vid_builder.py#L34) or [replacing it altogether](https://gitlab.com/Ragnyll/breaking_move_editor/-/blob/3b9dccc4c8e8883126237420ebd4aef14d57ac8e/source/move_vid_builder.py#L80).
 I even thought the json file itself would be useful for keeping notes on, but I didn't use any of these.

 Features are great and all, but theres no point of them if they are never used.
 Of all the features I had, I would only use it to crop videos, and of all the things I could do with the json object, that was the one that required the most actions that involved looking at other programs.
 I would go down to the file browser to get the path, copy it up to the editor, then pause the video to figure out what timestamps to start and stop on, then transcribe them onto the json object.
 One of those steps had to go, and the choke point seemed to be editing the json object.
 _If I could cut that out the pains of editing the text file would go away._

&nbsp;&nbsp;&nbsp;&nbsp;The trick would be where to do my trimming of the clip, and so I figured, why not do that within the file browser itself?

## Something Completely Different
 Thankfully the Ranger file browser provides a good interface for extending its functionality with [Custom Commands](https://github.com/ranger/ranger/wiki/Custom-Commands) and [Plugins](https://github.com/ranger/ranger/wiki/Plugins).
 So, what I decided to do was to write a plugin for editing videos in the file browser.
 It would contain 2 major functions:
*  Cutting videos in clips
*  Joining multiple clips into a single video

&nbsp;&nbsp;&nbsp;&nbsp;Those two functions were the only ones I used, so I made them the 2 functions of the program.
 In Ranger I could call `clip_cut {start_time} {end_time}` (or the functions it was composed from (`clip_start` and `clip_end`)) directly on a video I was viewing directly to the right of it.
 Calling this function writes the clip into a `clips/` directory that I can call `clip_join {output_fname.mp4}` on.

Cutting Clips | Joining Clips
------------- | -------------
![cutting_clips](/assets/2021-03-06-rewriting-my-video-editor/cutting_clips.png) | ![joining clips](/assets/2021-03-06-rewriting-my-video-editor/joining_clips.png)

&nbsp;&nbsp;&nbsp;&nbsp;These actions work significantly faster for me than editing the stupid fuckin' json file. It's a 2 step process of cutting a clip then joining multiple together as opposed to the 12 step (it might be more or less than 12; I never counted) process of doing all the file editing before to prepare the json file.
 The only disadvantage is the [`ffmpeg subprocess`](https://gitlab.com/Ragnyll/ranger-video-editor/-/blob/544ba9bf2c57aa9fd691da10d8f973304fe2bd59/video_editor.py#L46-51) that runs under the range command is a blocking process.
 Technically, that could be a long time, but for my main use of of cutting 30 or 45 second clips I was never annoyed by it.
 I'm sure there will come a point where I get annoyed so I'll find a way to background it then, but I'm still happy by the time saved this way.
 [`clip_join`](https://gitlab.com/Ragnyll/ranger-video-editor/-/blob/544ba9bf2c57aa9fd691da10d8f973304fe2bd59/video_editor.py#L126) on the other hand takes next to no time because ffmpeg is cool like that.

&nbsp;&nbsp;&nbsp;&nbsp;Here's the program in its entirety: [ranger-video-editor](https://gitlab.com/Ragnyll/ranger-video-editor/-/tree/master).

&nbsp;&nbsp;&nbsp;&nbsp;It's pretty damn small, but if you use ranger (or just hate clicking around some shitty adobe program to do something as simple as cropping and joining videos) you might like it too.

## Closing Thoughts
&nbsp;&nbsp;&nbsp;&nbsp;Beyond the obvious quality of life improvement this has has afforded me on my video editing, it's worth at least shortly talking about the value of simplifying complex manual processes.

&nbsp;&nbsp;&nbsp;&nbsp;Whenever I was editing that json file I was jumping around tmux and mpv like a madman.
 It was a pain to get that json object right the first time through and involved _a lot_ of typing.
 By bringing the process I was performing much closer to the files I was operating on the pain abstraction introduced went away.
 There was less typing, because I wasn't going through an external interface to work with the files I was touching.
 Not to mention the commands became way simpler (like `clip_cut` and `clip_join` are way easier to remember than many options on the json video editing program).
 I guess the moral of the story here is _when something annoys the shit out of you find a way to make it simpler._
