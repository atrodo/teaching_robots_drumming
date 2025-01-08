---
layout: post
title:  "2024: Teaching Robots how to Drum"
alt_title: "Little Drummer Robot"
song: "Little Drummer Boy by Lindsey Stirling"
---

Its once again self reflection time, and this year I have more than usual to
reflect on. While I did write one blog post along with a flurry of work at the
beginning of the year, the unfortunate reality of the second half of the year
slowed progress down significantly.

As usual, some hard statistics to start:

* Made 18 commits
* Changed about 18 files to the tune of around 1200 inserts and 140 deletions
* Wrote 1 blog post

After the last blog post, work on the robots got slowed down by a very large
rabbit hold of (successfully) bringing the WebGPU native API to perl. It all
started because I saw that WebGPU native had a standardized C header file, which
I felt was a good challenge to port. After all, perl does have some pretty good
tools for accessing C code directly.

After that I was able to start working again for a few weeks. After that though
was what really slowed me down: a move that sucked all of the free time out of
my fall. All of that has finally started to settle down here at the end of the
year, and I'm starting to slowly regather the codebase back into working
memory.

That does mean that there is not a significant amount of difference from the
last update. I am still in the process of rebuilding parts of the robot's brains
to understand 8 note scales properly instead of the 12 note confusion I had.
That process is taking quite a bit of time, and there's enough bits that all
assume/require 12 notes that getting all working in one go takes some effort to
say the least.

So here's to a new year and some new robots learning how to drum.
