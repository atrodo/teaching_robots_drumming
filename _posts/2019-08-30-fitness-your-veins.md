---
layout: default
title:  "Fitness Your Veins"
song: "Into Your Veins by Five Iron Frenzy"
---

I discovered some bugs with the fitness functions, the automated verification
process used to evaluate the neural networks on basic attributes of each band
member like being on beat or following the melody. While the symptoms of the
bugs were easy to spot, like rapid notes and not filling the entire time, the
causes of these bugs were not obvious at first. I had to dig into the data to
discover the issues.

This bug hunting process has been a good reminder that having a good
understanding of the problem space is important. The neural networks and fitness
functions are not magic. They require work, work on my part, knowing how to
massaging the input data and how to evaluate the results to get good results.

The first issue I found was in the fitness function I use to make sure band
members followed the melody. What I realize was that I was only checking a
single sub-beat, or 1/12 of a beat, followed the melody. This resulted in very
rapid notes being played because the neural network was being rewarded for
keeping the note on for only 1/12th of a beat and penalized for keep the note
on.

I changed the code so the test melody was between 4 and 12 sub-beats long. Then
updated the fitness function so the neural network gets rewarded for playing a
note the overlaps the test melody and penalized if that note is too far off. The
penalties become harsher the more egregious it is. For instance, if it lasts a
sub-beat too long, it's not penalized at all. But if it's a whole beat longer,
it gets heavily penalized.

I also found out that the band members would stop playing in the middle of a
song. I had limited the fitness checking process to only evaluate a limited
number of beats because otherwise the training time would increase
significantly. So the test melody was extended to also include and evaluate on
the end of a song as well. Initially the neural network code got very confused
by that because it assumed beats were strictly incrementing, but I fixed that
code as well after some human confusion.

I also started applying the test melody to all the fitness tests. I had
previously not since some functions don't need the melody to test, like if it
was on beat or not. What I discovered was that adding a melody awakened unused
parts of the neural network that would cause it to perform poorly.

Then to allow the neural network to recognize new notes that abutted the end of
old notes, I added a change that would allow the neural network recognize that
difference between a note, the end of a note, and silence.

With all of these fixes finally in place, the fitness function was achieving
fitness in a reasonable number of generations and the results from the fitness
were looking good on on paper. So I generated a new song from the newly trained
artists. The band was acting better, however I still had an issue where
everything was done very quickly.

As I started digging in again, I realized that when I imported the melody, I had
multiplied the beat counts by 12. Correcting that made things behave better. Not
that the changes were unnecessary, because I actually have quite a bit more work
to be done on the fitness functions.
