---
layout: default
title:  "Sing a new Robot"
---

I am just wrapping up a database reorganization that introduces the song table.
So far, my monster of a main script has been doing that work, but now that code
can finally move into a new home. Took a couple iterations, but I think I've
settled. Originally the structure was going to just point at the band and
generate the wav from that. But then it wouldn't be repeatability as the neural
networks would change over time and produce different outputs. So while I
maintain a reference to the band that created the song and each track, I went
farther and record the data structure used to generate the actual music.

In short order, I'll be able to do something along the lines of
```$band->new_song``` and get out a fresh, new song. Along the way, I'm doing
other mundane things like allowing a band to automatically create the related
artists and neural nets. I'm still trying to figure out how the whole process
works together, because I'd like to allow artists to move between bands and
"collaborate". Right now the vision is to have a stable of bands, each with a
current line up. As each of those bands make songs, hopefully I'll have a
process in place to allow each artist to grow and as a result make each band
better. Unfortunately that whole process is just as hand-wavy as this
point because there are a lot of outstanding logistics.
