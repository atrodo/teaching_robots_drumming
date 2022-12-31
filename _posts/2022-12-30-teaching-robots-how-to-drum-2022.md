---
layout: post
title:  "2022: Teaching Robots how to Drum"
alt_title: "I Hate Rhubarb Parties"
song: "I hate Christmas Parties by Relient K"
---

It has been a long year, and in this final week of the year, it is time to take
stock about what has happened. As is tradition, I'm also taking a moment to
apply this process to Teaching Robots how to Drum.

First, some hard statistics:

* Made 40 commits
* Changed about 30 files to the tune of around 2,400 inserts and 600 deletions
* Wrote 3 blog posts
* Wrote about [My personal tenets of fitness functions]({{ site.baseurl }}{% post_url 2022-03-31-tenets-of-good-fitness-functions %})
* Gave a [couple]({{ site.baseurl }}{% post_url 2022-07-31-recording_robots_drumming %}) [updates]({{ site.baseurl }}{% post_url 2022-11-07-mundane-update-mundaneness %}) blogs

What those hard statistics don't represent is how much work actually did happen
this year. Some of the most important items that come to mind include:

* Began regular uploads
* Continued to do regular uploads for 6 months
* Worked on improvements to both the code and process
* Improved the robots understanding of musicality
* Invested in to making the robots sound better

It is unfortunate that all of this work does not have something more to show
for. I would not call the robots having a significantly better sound today than
it did 6 months ago, but I will say that I feel closer than I did 6 months ago
about cracking the musicality nut, as it were.

Since my last post, having taken a small break from figuring out the fitness
issues, I started to tug at a new thread. Once I cleaned up some code, I started
to look at why the fitness function that told me if a note was being musical
would not always be exactly 100% accurate when copying input values to output
values. What I ended up discovering was that the input for the neural network
and fitness functions were not on the same page of when a note started or ended,
nor which note was considered correct at any given moment. While the vast
majority of the time the two would agree, there were several edge cases where
this was not true.

Once I got the edge cases smoothed out and both the neural network input and
fitness functions on the same page, I discovered that my previous issue had
gotten worse. I could now get the fitness function to show 100% accuracy using
my input/output bypass, but could no longer get the training to raise to the
fitness levels of robots to an acceptable level. I started to once again tug at
this thread, and I noticed that no matter what happened, the notes selected by
the robots were never more than random. I put a filter in place to check that
some percentage of the notes selected were in the correct bucket, but the robots
just could not come to fitness.

It was at this time that I finally realized what was happening in my last post;
it wasn't that the combination of fitnesses were not compatible. Instead another
error was allowing a fitness to appear as working because of a broken
methodology.

This has lead me to add a new tenet to my list of good fitness functions. This
new tenet is as such:

* Fitness functions should be given a linear funnel to obtain values

This means that in order for a neural network and fitness function to function,
it must be able to produce values close to the correct value and have them
rewarded for it. I have updated the original post with this new tenet.

What had been happening is that each note was evaluated on if it was a good
note or not, the function is asking the question if a note the robot produces is
what the system considered a part of the current chord. The thing with chords,
however, is that they are never linear, never next to each other: B-flat and B
will never be used in the same chord. This meant that the robots had to always
guess the right note, being close meant being in a completely different part of
the chord spectrum. The robots could never get close and have partial credit
because the neural network output was always note order linear, B and B-flat are
always next to each other.

After discovery, the code was rewritten so that the output of the neural network
now has a new knob for the robots to change. It gives them the option to output
a single direct value, like it has been doing, or to indicate a different
spectrum of note values to be used. This had been an idea I had floating in my
head for a while, but it was a bit complex so I hadn't don it yet. After
discovering this issue, I had not choice but to implement. Initial tests are
showing positive outcomes.

As we barrel into the new year, I am in a bit of a different place than previous
years. I am not entirely sure what 2023 holds for Teaching Robots to Drum. I
also cannot wait to find out what does happen.
