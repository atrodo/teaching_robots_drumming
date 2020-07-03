---
layout: post
title:  "FFmpeg and libtheora Part 2: Multi Stream Ogg Chaining"
alt_title: "Chain of Rhubarbs"
song: "Chain of Fools by Aretha Franklin"
---

After my last post, I was finally able to save my samples back to `Ogg` once
again, with a good amount of speed. This has enabled me to once again begin
streaming these samples in order to evaluate the robots progress. With my new
found extra speed, I set about realizing my goal, only to find out that there
are more obstacles.

So my first order of business was to to make sure I had as much speed as I could
from `libtheora`. I knew work was more focused first on `Daala` and then `AV1`,
so I wasn't expecting much. What I discovered was that the code had not
generally been touched for 10 years, but between the last official release of
1.1.1 and today there is an unreleased alpha of `libtheora` that included more
speed optimizations. I was able to compile this 1.2-alpha version and got two
new speed levels to choose from. With level 4 I was able to get about 2-times
real-time speed, which closed the gap with `x264`. I was now very happy with the
speed of `libtheora`.

What I wasn't happy with was that the stream would not play after the first
file. I started investigating why in the `ffmpeg` logs, and quickly found my
error:

>  Changing stream parameters in multistream ogg is not implemented. Update your FFmpeg version to the newest one from Git. If the problem still occurs, it means that your file has a feature which has not been implemented.
>  failed to create or replace stream
>  Not yet implemented in FFmpeg, patches welcome

I was a little confused by this error since I had been playing audio previously.
Even so, I started digging into what was causing this issue and why I started
getting it. Since I was working with a six month old version of `ffmpeg`, I
suspected getting a newer version from `git` was unlikely to fix my issue. Since
I knew that `ffmpeg` doesn't give as much attention to `Ogg` based formats, I
decided instead to dive into the `ffmpeg` code to see where that error was
coming from and what actually causes it.

I ended up finding the code where that error comes from; it was in a function
called `ogg_replace_stream` which sounded like exactly what I was doing. I
investigated it and saw code that seemed to do everything that I wanted.
However, it would only do what I wanted in two limited conditions: the stream is
seekable or there is only one stream. The `ogg_replace_stream` function [had a
comment above it describing its indented
purpose:](https://github.com/FFmpeg/FFmpeg/blob/n4.2/libavformat/oggdec.c#L203):

```C
/**
 * Replace the current stream with a new one. This is a typical webradio
 * situation where a new audio stream spawn (identified with a new serial) and
 * must replace the previous one (track switch).
 */
```

So I started to investigate what this meant, and that search lead me to the
[official `Ogg` documentation](https://xiph.org/ogg/doc/oggstream.html). From
reading this, I discovered that `Ogg` has a feature built-in called chaining;
concatenating two files together with no breaks results in a logically correct,
continuous stream. Audio stream servers like
[`Icecast`](https://www.icecast.org/) use this to their advantage; to play the
next audio track, all it does is start sending the next `ogg` file. Since this
is such a common usage, `ffmpeg` makes a special case for it.

Unfortunately, in my case, I am producing `Ogg` files with both audio and video,
which `ffmpeg` does not support chaining `Ogg` files with more than an audio
stream. This means I can't play more than a single file before the stream ends.
From what I can gather on the internet, `ffmpeg` solution is a common way of
handling chaining.

In fact, it's not just `ffmpeg` that has done this, I found that [FireFox had a
bug](https://bugzilla.mozilla.org/show_bug.cgi?id=455165) related to exactly
this.  Unfortunately, it appears that FireFox wasn't able make it work with all
the edge cases. However, since I am able to control all the inputs, I decided to
take a stab at fixing it anyways since I had already modified `ffmpeg` once.

I pulled up the newest version of `ffmpeg` from git and got to work. Once I had
it, however, I noticed that `ogg_replace_stream` looked a bit different from the
older version; someone had worked on it somewhat recently. I checked, but the
issue was still there.

After some a few readings of the code, I had convinced myself that it appeared
that everything that needed to be done was there; it was resetting the
parameters and letting the other code in `oggdec.c` take over and set the
parameters correctly from the stream. So I took a couple evenings to try and
change the code to allow this to happen for multi-stream `ogg` files.

This was not without difficulty; what needed to happen was not obvious nor were
the source of the errors that came from my changes. My goal was to change it was
so that it would accept any new stream as long as it was replacing a stream with
an identical codec and would do the same reset that the code was already doing.
The code in `libtheora` however was not happy; every time it would error out
with an `AVERROR_INVALIDDATA`. That was the bug that took the majority of my
time to track down.

I finally discovered that that `libtheora` would error out because it thought it
had already been initialized even though I thought I had cleared its state.
After gathering how I thought `ffmpeg` initializes the stream, I had to make
sure to undo all of that so that `libtheora` could redo its initialization. The
existing code however wasn't clearing the private buffer that `libtheora` was
given; even after reallocating that however the code was still returning a
`AVERROR_INVALIDDATA` error.

After a lot more digging, I discovered that the `libtheora` wrapper code in
`ffmpeg` actually stores stream specific data in two places; one is the stream's
private data and the other is in the codec parameter's extra data. Once I got
that cleared in both places, things appeared to work; or rather they work well
enough to work for my use case.

The code I produced was pretty specific to my works-on-my-machine type of
situation and I know I have some code deficiencies; for instance I think the
code should sure that all the `Ogg` streams are closed when it starts replacing
any of the streams. Because of that, I haven't submitted these changes to the
`ffmpeg` team. Instead I have opted for having a simple [GitHub
fork](https://github.com/atrodo/ffmpeg) that contains all my changes.

In the end I finally got everything work well enough for what I need to do with
it. Although, I was occasionally still getting errors about bad CRCs and other
mysterious errors, nonsensical errors, but we won't talk about my missing
`return` statement that allowed data to be written in the middle of another
stream. That's just embarrassing.
