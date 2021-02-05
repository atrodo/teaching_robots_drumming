---
layout: post
title:  "Discovering Microcosms Between the Beats"
alt_title: "Like Robots I Missed"
song: "Like Something I Missed by Five Iron Frenzy"
---

Recently, I started listening to more melodies and soon discovered that some of
the melodies were not nearly as consistently transcribed as I thought. Some
songs came out very well done, while others had a more soup-like consistency. I
had to take some time again to dive in and find what the problem this time was.

While the notes being played were technically correct as far as I could tell by
inspecting things, they did not sound right at all. The biggest problem was an
audible clicking and distracting random note dancing. It was, very literally,
not musical at all.

So I broke out my debugging rig again and dug into what was going on. The first
thing that jumped out at me was the duration of notes were being played at; the
durations seemed really short. I had already put measures in place to make sure
that every note was at least an eighth note in length, but the length in
milliseconds seemed really small. I did my maths again but the numbers I came up
said that notes that short is what I should expect.

Then I started to manually track songs, looking at how the robots saw the melody
and listening to it with the errors, trying to find the melody visually. It was
at this point that I started having real problems diagnosing the problem; my
manual tracking was lining up with the music a lot better than the sound it was
producing was.

So I went back to how short these notes were. I decided to triple check myself
and my math and so I searched the internet to see if I could find the range of
milliseconds an eighth note should be. On one of my first search iterations I
came across [Pulses per quarter
note](https://en.wikipedia.org/wiki/Pulses_per_quarter_note). Once I read it the
light switch went click in my head and I knew what my problem was.

When we talk about Beats Per Minute, or BPM, we measure how many beats, or
audible increases in sound, happen in the span of one minute. Music tends to
average between 100-200 BPM. If you ever took a music class, you were introduced
to 4/4 time, that is 4 beats to a measure, the quarter note getting the beat.
You may have also heard about 6/8 time, that is 6 beats to the measure, the
eighth note gets the beat. That lower number, 4 or 8 in the examples above,
tells us what notes on the sheet music get this audible increase in sound that
happens at regular intervals and establishes the rhythm of the music.

Instead of doing all my calculations assuming a beat represents a quarter note,
I had it stuck in my head that a beat represented a whole note or downbeat.
I'm embarrassed that this didn't dawn on me before. As someone who has a good
basic understanding of music, I knew better than that. Unfortunately I had
gotten so caught up in connecting all the pieces together, that I didn't take
the time to think about all the meanings.

The thing that really made this change in thinking difficult to catch was when I
would manually track the song I would find these microcosms of bad notes that
would look just enough like the melody. I'd then operate like this small space
of melody was actually the larger piece and I'd do my math based on it. My math
would look just close enough to not require more review, so I moved on.

The upside of all this means that things are not completely broken, I was just
manually tracking at too fine of a level. I'm going to adjust the code so that
there is a longer minimum, and initial tests are looking promising. I'm not sure
yet if I'm going to start releasing music before or after I get more of the
planned songs to be recognizable.
