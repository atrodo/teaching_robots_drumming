---
layout: post
title:  "Progress Update"
alt_title: "Far, Far Robot"
song: "Far, Far Away by Five Iron Frenzy"
---

Soon after my last post, I had finished the manual fitness tests and got them
all working enough to start listening to some output. The problem became,
however, that even though my automatic fitness checks were saying that the
output was good, the audio told a different story. While the rhythm was being
followed to some degree, it was not nearly as good as the fitness functions said
they were; in some cases it was playing notes that did not sound well at all
together but saying that it was following the notes of the original melody
well.

That set me back, not in terms of work, but in motivation. So I took the
opportunity to work on a couple of other projects while I let these issues
simmer in the back of my head.

Once I got back around to it, I had a couple of plans. The biggest was that I
wanted to verify my note fitness function. In music, notes next to each other on
the keyboard don't sound good when played together. There is some simple math to
know which notes sound good together. I am using the [Circle of
Fifths](https://en.wikipedia.org/wiki/Circle_of_fifths) extensively to make sure
that notes sound good together, and my code was suppose to make sure that good
sounding notes are rewarded. After some cleanup and adjustments, I was able to
verify that notes were being rewarded correctly. That was a little
disappointing.

My disappointment was short lived, thankfully. As I worked through more of my
ideas, I came to the code that feeds the neural network. That is when I
discovered that it was not actually being feed the notes to the neural network,
correctly at all. So the fact that it was doing as well as it was is a little
amazing. Once I corrected that, everything started looking a lot better.

One of the small optimizations that made a larger than expected impact was how I
handle the default fitness melody. For efficiency, I had a melody that was
generated with a series of loops and counters. Originally I made it so that it
would feed the first and last 20 seconds of this melody into the neural network.
This made training against it fast.

The problem, however, was that the other melodies that I was training against
was a full 3-5 minute song, and took up to 10 times as long to evaluate. I
decided that instead of making the training song special, I'd add a function to
trim the middle out of a song. That sped up the whole process for all of the
songs I was using as input. It also had the benefit of being able to import this
default melody into the database to do other tests against.

At the end of all of this, however, there's not much new. I've cleaned some of
the fitness up and everything is working better. I've started to really try and
get into the manual training loop and optimize that loop. I think most of the
processes are working well enough that I'm starting to work towards the next
milestone: generating covers of songs that are not completely embarrassing.
