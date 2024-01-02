---
layout: post
title:  "2023: Teaching Robots how to Drum"
alt_title: "Robots Rest Ye Merry Gentleman"
song: "God Rest Ye Merry Gentleman by Lidsey Stirlin"
---

Another year, and another New Years blog. This year, however, is a bit different
since there have been no other blog posts this year. It is a bit representative
of the past year as there was not much teaching of robots happening for most of
the past year.

First, some hard statistics:

* Made 31 commits
* Changed about 48 files to the tune of around 6,500 inserts and 400 deletions

This year had a bit of unfortunate turn of events. Towards the beginning of the
year, I fell into a deep rabbit hole of perfection; what was being produced was
all of a sudden well below expectations. It was at that point that I was feeling
burnt out and decided to take a short break to work on some other projects.

That short break ended up lasting most of the year as I ended up working on
multiple other side projects. At one point I was at 4 additional projects deep.
While I'm still slowly digging out of all of those projects, I have been able to
finally returned to working on teaching these robots to drum.

To actually start tackling how to tune and improve the training process, I've
taken the time to reorganize the streaming features I first created in 2019.
This time, instead of streaming an entire song from start to finish, there is
now an intelligent process that ensures that the pipeline is filled with a raw
audio stream. This allows for a uneven amount to be played while I figure out
what's going on and what I need to do. To get this far, I also needed to work on
cleaning up some of the uncommitted code and add some more niceties.

The most interesting bug to come from that was the case when it would read/write
an odd number of bytes. Since the stream input is in 16-bit raw PCM data,
writing only 1 byte instead of two would cause a misalignment. Normally, it
would work fine since the next read will cover the odd byte, however if the odd
byte was the last in a file, and the next byte was in a new file, for example
when swapping files, then static would erupt. I discovered this because I would
be listening to the 5 second silence file, and randomly it would get replaced
with static.

And that's pretty much where I'm at at the end of 2023. It is unfortunate that
more had not happened, but I can only look forward to doing more in the upcoming
year.
