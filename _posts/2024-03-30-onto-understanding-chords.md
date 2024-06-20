---
layout: post
title:  "Onto understanding Chords"
alt_title: "Robering (Don't Turn Arobot)"
song: "Sobering (Don't Turn Around) by Plumb"
---

Since the start of the year, I've been working, on and off, on getting the
robots into more specialized lessons. I have a much improved process that allows
me to listen to a single robot's progress, figure out which one is causing
issues and make adjustments.

I started off by really diving deep into the fitness for the drummer. Two things
stood out: the volume was much louder than expected and the drummer would
sometimes rapidly drum. Code was added so that a new limit-only type of result
from fitness checks could be utilized. It cant cause the fitness score to change
alone, but instead it is used to set an upper limit on the score. This meant it
could be used to stop extreme issues that are undesired but would otherwise
allow the robot to score well. I used that to help the rapid drumming by
reducing the max score every time the robot did not wait long enough between
strikes.

The volume however took a bit more time. What I ended finding out was that the
stats being calculating used a circular buffer of values. It was a 21 element
array to hold how loud the robot was, with 0 being the middle, and positive
numbers 1-10 being louder and negative numbers -1 to -10 were softer.  The scale
would adjust based on the requested volume; if the fitness wanted -3 it would
shift the elements so -3 was in the middle. But when requested to be very soft,
-10, since the array was circular, +10 would end being used as "softer than -10"
and scored really well. Doubling the size of the array and putting sharp
negative values past the "end" of the array helped curb that problem.

The drummer was now in good shape so I started to investigate another artist.
As I started working through it I decided to revisit some older code that would
import a transcribed song and chords. The plan was to update it with new
concepts I had introduced since I originally wrote the import tool. The idea was
it could enable me to import a song I could more easily recognize so it would be
easier to hear how the robots were doing. In the process, I began to question
what I had been doing with chords as I was diving deeper and deeper into chord
theory.

As I started to write the code that understood the chords coming from the
transcription, I ended with an "Ah Ha!" moment that finally connected the circle
of fifths with chords. Every time I had looked at the wheel, I saw the C -> G ->
D in positions 1, 2 and 3. My brain went ahead and connected them as 1st, 3rd
and 5th; the major triad. The problem is that the major triad is actually C -> E
-> G; I hadn't taken the time to make sure the notes were actually right.

Interestingly enough, which should have clued me in to the issue, each note in
the circle of fifths is 7 semitones apart.

Once the revelation happened, I slowly started to make adjustments. The wheel of
fifths started getting replaced with an 8 note scale. Then the scales being
inferred based on existing data. Then feeding that inferred scale into the
robots instead of a wheel of fifths. Things were not entirely simple either,
much of the code I was dealing with assumed a 12 note circle of fifths, not an 8
note scale. Then was the hard part of making sure it all worked, and worked
correctly. That bit I'm still working through, but I'm pretty confident I'm on
the right track again; at least until the next major misunderstanding appears.
