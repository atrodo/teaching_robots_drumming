---
layout: post
title:  "FFmpeg and libtheora Part 1: From Slow to Faster Encoding"
alt_title: "Speed of Tedium"
song: "Speed of Love by Owl City"
---

After my journey in making a process to create and manually evaluate samples, I
started working on the actual mechanics of how to get a rating back into the
system. After some initial flawed designs, the new, better design sent me down
a rabbit hole debugging `ffmpeg`, `x264` and `libtheora` until finally coming to
the point where I can generate and evaluate all the samples I want.

Initially, I was planning on just playing each sample one after another, but
since each sample was only 20 seconds long, I came to the conclusion that I
wouldn't be able to rate them fast enough to apply the rating to the correct
sample. So I changed my approach and repeated the sample, followed by some
silence, until I rated it. It was more code, and a new module, but it was less
code than I expected and the outcome will be much better in the long run.

While doing that, I had been able to determine for sure that I was having an
issue with the streaming. Up until this point, most of the streaming would be
short, because I was generally debugging something else and restarting the
stream often. As the stream got longer and longer and I started to actually use
the stream, I noticed that things would stop working. Specifically, in the above
repeat loop, the repeated sample would never play and the program would just
freeze.

So I broke out good-old `strace` and saw that the audio-pump, the process
responsible for pushing the audio to the stream, was blocked on writing. I had
came across this before when the receiving side of the data was slow in
processing the data. To try and clear it temporarily, I adjusted the pipe to be
non-blocking. This ended up unfreezing the entire process, however, pulling up
`strace` again, I saw that it was continually get an `EAGAIN` error. The pipe
was still blocking and not clearing itself; the other side was simply not
processing data.

The other side in this case is `ffmpeg`, which I use to do all of my media
processing. I looked more closely at my `ffmpeg` logs, and I noticed that I was
getting this error:

> moov atom not found

I did some searching and discovered that this was caused by my recent change to
using `MP4` for encoding my samples instead of `Ogg`. The reason for that change
was strictly based on encoding speed of video; `x264` was encoding at about 2.5x
real-time while `libtheora` was encoding at about 0.4x real-time. In other
words, for a 20-second sample, `x264` would take 8 seconds to encode while
`libtheora` would take about 50 seconds.

This speed discrepancy was something I had been struggling with. While I could
believe that `x264` was faster considering all the work that team has poured
into it, I couldn't believe that `libtheora` was slower than real-time.
Searching online I couldn't find anything talking about `libtheora` being so
slow, so for a long time I just figured it was me, my machine or somehow
something specific to my system causing the slowness.

Now that I'm encoding a lot more, the speed difference was becoming a big
obstacle.  I have always been a huge fan of Xiph and the work they've done on
providing open and patent free formats, so using `Vorbis` and `Theora` is
important to me. Reluctantly I had made the pragmatic decision to move on to
`MP4` with `x264` to gain a hefty amount of speed.

It turns out that I had forgotten that `MP4` is not a streaming container; it
was designed for applications where you can read all of the data at once or are
able to seek within the file. As such, I can't just write the next file to a
pipe and let `ffmpeg` deal with it.

The `MP4` container format has atoms in its structure that describe the data and
that structure. One of those atoms gives the general description of the video
and its characteristics; this is the `moov` atom. It can generally be found
either at the beginning or end of an `MP4` file. By default, `ffmpeg` puts the
`moov` atom at the end because that's easier since you have to encode the entire
file to know what to put in the `moov` atom.

Since `ffmpeg` is able to reserve space at the beginning of the file and write
to that space at the end of the process, my first attempt was to use that
option. After some quick testing, however, it was obvious that this solution was
a non-starter. Putting the `moov` atom at the beginning created a new error when
I tried to send two `MP4` files in a row to `ffmpeg`:

> Found duplicated MOOV Atom. Skipped it

I discovered that even if it can find a `moov` atom, once `ffmpeg` saw the next
file it would ignore it. That was when I decided that I couldn't use `MP4` as
my storage format for encodings. At this point, my options were to continue to
use `x264` with another container format or try to make `libtheora` work. My
other container options appeared to be `Matroska`, `MPEG-TS`, or `FLV`. However,
all of these seemed less than ideal for using as both for streaming and
permanent storage. I decided to dig into why `libtheora` was slow.

My first thought was that I didn't have hardware acceleration enabled when I
compiled `libtheora`, so I started poking around the source to figure out how I
could confirm or disprove that idea. While poking around, I discovered an
example program that would take a `yuv420` input file and produce an `Theora`
file. This was a perfect opportunity to test the speed of `libtheora` without
`ffmpeg`. Unfortunately, the speed was similar to the speed of `ffmpeg`, however
I noticed a "speed" option available.

So I reran my test with the speed setting cranked it up to 11, only to find out
the option only goes up to 2. At level 2 it was going at about 1.5x real-time,
which was fantastic news; this meant there was hope for making `libtheora`
faster than real-time. However `ffmpeg` does not expose this option for
`libtheora`; in fact, `ffmpeg` exposes no options for `libtheora` outside of the
standard options. It does, however, have a lot of options to configure every
detail of `x264`. Take that for what you will.

Since I had the `ffmpeg` source, I went ahead and added code that set the
`TH_ENCCTL_SET_SPLEVEL` option inside of `ffmpeg`. Since I have very specific
concerns, that is speed, I decided not to make it configurable, and instead just
set it to the max number.

At this point, I was back to using `Ogg` to save the audio and video samples and
it looked like it was smooth sailing to start streaming. Unfortunately, that was
not the case and there was more to take care of before I could claim victory, so
this story is actually continued in part 2.
