---
layout: post
title:  "Adding Variety to Robotic Listening"
alt_title: "Never Break The Section"
song: "The Chain by Fleetwood Mac"
---

It has been more than a few months since I finished reflecting on [my robot
making music experiment]({{ site.baseurl }}{% post_url
2021-05-15-initial-conclusion-robots-learning-drums %}). Since then, I've been
working towards a few goals to get back to daily video uploads. Even though it's
its taken longer than expected for various reasons, several unrelated to the
project, I am almost back to that place of generating daily video uploads.

After listening and making notes about all of the robot produced music, I was
able to identify a myriad of problems. After working through the list, I was
able to put every issue into two major categories: Beats-Per-Minute (BPM) issues
and Musical Variety issues. From there I was able to brainstorm some solutions
to improve the musicality of the robots.

For the BPM issues, it didn't take me long to discover that the source of my
issue was easy to identify: myself. When I first wrote my beat detection code, I
knew that the BPM of a song is not universally constant, even if it is
generally constant. I wanted to be flexible upfront and allow the BPM to change
over time. The problem however was that the detection method I had written would
often seriously misidentify the BPM of some segments resulting in both extremely
long as well as sections of rapid chattering. Because making it significantly
better is going to be a good amount of work and the current beat detection does
a good job in general, I decided to just remove that initial flexibility and let
the code find and use a consistent BPM for the entire piece. I'm hoping to
revisit this in the future to accommodate when a song takes a noticeable BPM
shift, but for the time being, this will serve me well.

The next piece was the variety issues, and by variety I mean that the songs the
robots created lacked it; they played the given notes at a somewhat constant
volume, and there wasn't much more than that. In order to help the robots figure
out that they can play other complimentary notes at different volumes, I figure
I need to give the robots more data and more goals. To achieve that, I split
things into those two categories: data and fitness. For data portion, I wanted
to extract more data from what the robots hear. With the goals portion I wrote
brand new fitness function to operate on the new data.

So I went back into the melody extraction code. This time I didn't work on the
melody extraction itself, but instead focused on information around and above
the beats themselves. I already have a rudimentary beat tracker in place, so I
worked on adding another system on top of it that looks at each of those beats
collectively and split it into bars and sections.

To establish a bit of terminology, bars are consistently sized groups of beats
that form a regular rhythm reference, generally between 2 and 8 beats long.
Sections, for the purposes of my code, are irregularly sized groups of bars that
contain similar elements and denote where difference in the music are
significant. If I was to compare them to writing, then I would say that beats
are words, bars are sentences and sections are paragraphs.

Both bars and sections have different requirements and require separate
approaches. Since bars are of a set length, I ended up using a method based
around the idea of identifying the downbeats by looking at nearby beats to see
if it is the loudest within a bar-sized range. After doing that for each bar
size, it chooses the best bar length and worked backwards from there, filling in
each downbeat assuming the loudest beat is a downbeat.

Sections were more complicated to handle since they don't have the same regular
sizing like bars do. What I decided on was to go through each bar and look for
noticeable differences in statistical information between bars. Although the
method is extensible, right now I'm primarily only looking at how loud each bar
is as well as the predominate notes of each bar. When either thing changes
significantly, I start a new section. I know this method has several available
improvements, but right now the most important feature of it is that it works
well enough for the time being. This too I plan to revisit in the future.

Once all the code to find bars and sections was written, I went ahead and also
threw in statistical information about each bar and section; notably the
loudness and frequency of notes and various metrics available after the melody
and bar extraction processes. The aim is to allow the robots to use these values
to drive less deterministic decision making instead of just throwing random
numbers into the mix and hope things don't got completely crazy.

As for second half of the question, fitness, it ended up being a bit more
straight forward. In fact I was already able to write a couple new fitness
functions based on the new data pretty quickly and the rest that I'm planning
don't look particularly difficult. The creation of them was straight forward
enough because of all of the new points of information available. Beginning to
use and tune the robots with them however isn't complete yet, but that's going
to take a bit more time and experimentation.

I did run into a big issue in bringing that data back into the neural network,
not so much because it was hard to bring that data in, but more because adding
that additional data in meant that the current neural networks become
effectively invalid. That is because the inputs are no longer fixed values and
the network began to respond to the new values that it didn't before. That's an
unfortunate reality in that every time I add new data the neural network they
will need to be completely retrained. Since it's so early in the building phase,
it is not a huge deal yet. As time goes on however it will become much more of
an issue that I'm going to have to consider how to handle each time it comes
up.

After all of that, I'm happy with the progress I've made in the past several
months. I know most of what I've produced is not state of the art, but that is
okay because my goal is not progressing the start of art. That is the really
difficult part sometimes; remembering that my goal is not to have something
perfect the first time, but to have something good enough to be entertaining.
