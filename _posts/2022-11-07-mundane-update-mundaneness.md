---
layout: post
title:  "Mundane update of mundaneness"
alt_title: "Sock Rhubarb"
song: "Sock Heaven by Steve Taylor"
---

Although it's been a few months since the last post and there has not been much
progress to report, I feel like the goal of this blog and project is to keep
going until I hit a point that it can be called complete. So in light of that, I
have been continuing to upload videos for months now and am working towards
improving the entire process. Unfortunately, I've been trapped in looking at
numbers again and making things works numerical, so it is time to redirect my
efforts.

Several weeks after I began regularly releasing songs, I started to dive back
into the loop of identifying a noticeable problem, identifying a fix, and
working on it. The first thing that stood out to me was that the drums were too
loud. What was extra frustrating about discovering that was the fact that I had
just worked on loudness and mentioned it in the last blog. I set to work on
figuring out what went wrong from the last time. What I discovered was that the
work I had previously done with the loudness fitness check worked well when
aiming for a large percentages of the initial loudness, but failed for small
percentages. This turned out to be due to how I was calculating the
normalization and denomalization loudness values. Once I got them to work more
consistently, as well as with more precision, loudness started working for
percentages big and small.

Then during the retraining of the robots after this update, I discovered that my
primary instrument robot was having troubles finding a good fitness. After quite
a bit of debugging, I discovered it was in this bizarre, difficult to solve
state. The problem is rooted in the fitness function such that it tries to get
the robot to do three things: match the current beat's chord, follow the beat
closely, and be at 70% of the total loudness. The loudness does not appear to
help or hinder the issue, so we can ignore that aspect. The pieces of data it is
attempting to match are direct inputs to the neural network. I have verified
that if I copy those inputs directly to the output, the fitness function indeed
gives the expected near perfect score. However, training appears impossible,
never even coming close to the desired fitness score. If each component is
trained by itself, either the chords or the rhythm, both quickly reach that
desired score. But if both requirements are enabled, the robot struggles to get
even a half decent score.

At first I though the issue was because the number of notes was way too many, so
I added an optional maximum note count requirement. That did not help the issue.
Then I started build in more debugging information, but none of it helped to
determine the source of the problem. I did try an experiment that progressively
trained each piece independently, slowly increasing the percentage of each
component until the desired score was reached. That experiment proved that this
problem arises as soon as I added both elements to bear. I suspect that a big
part of why that change reacted that way was because of the other change I had
already made.

In the process of doing all of this, I did make some other changes. One of the
more significant changes I made recently was capping the maximum score a robot
could achieve if any of the functions returned a very low score. Say, for
instance, one function was returning a 5%, but two other functions were
returning 90%. This made it appear that there was a very good result, when in
fact the bad function is noticeable. This change keeps the robots from having
the illusion that everything is okay, when in fact, the robots were just really
good at exploiting a single portion of the fitness. A later extension to this
feature was to change how scores were produced so if an attempt would have
scored higher without the cap, it would be sorted higher. This is because in
theory those attempts make a better source root for future generations.

I eventually decided to put the whole bizarre data situation problem down and
focus on other problems while I think about in the background. I started doing
some cleanup; looking at other fitness functions, doing some training where I
could with the changes made, and the like. At this point I'm taking a break and
exploring some of the other training options before picking this specific
problem up again.
