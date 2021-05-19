---
layout: post
title:  "Initial Conclusion of Robots Learning to Play Drums"
alt_title: "Rhubarb's Triumph"
song: "Mother's Triumph by Matthew Thiessen & The Earthquakes"
---

It has been 30 days since [I began releasing music]({{ site.baseurl }}{%
post_url 2021-04-15-launching-robots-learning-to-play-drums %}) created by
robots. It's be a very interesting 30 days, and I've learned quite a lot doing
it. I've already made and planned a couple improvements for the existing set of
uploads and am also looking at future improvements. With all of that that behind
me, I wanted to record briefly what I did do and some ideas and plans I have for
improvements going forward.

Initially I was hoping to do a larger, more comprehensive campaign around
posting to social media while analysing things that were happening and making
course corrections as time went on. I ended up deciding to do no social media
posts nor any major structural changes to the content, like changing video
items, thumbnails, or the like. Instead, I focused on establishing a baseline of
what to expect in the future with a few mid-course changes to the music
generation process.

The biggest mid course change was an adjustment to the data I fed to the neural
network. Early on I was using a basic on/off value to denote that the melody is
playing a note, with an additional value to indicate that the note is about to
finish to help the robots find the end of notes. The biggest mid-stream change I
decided to do was adding more data and use a sawtooth pattern between 12 values,
high to low. This was to indicate which subbeat of the melody note is currently
playing. This gave the drummers a much better opportunity to have short staccato
hits.

Then I discovered that my normalization for notes was not giving the robots an
accurate view of notes; notably they were being given weird values when the note
wasn't near Middle-C. I adjusted that so it is given a more honest view of the
note being played, and corrected the output so it was actually related to the
[circle of fifths](https://en.wikipedia.org/wiki/Circle_of_fifths) and not just
made up notes with no relationship.

Going forward, I have a list of ideas of change after listening to a couple
things. That doesn't give me a complete picture of the current progress, so I'm
going to earnestly listen to all 30 videos, take notes, and rate how much each
change could possibly improve it. I am hoping that in the process I'll come up
with more ideas and a solid go forward plan.

Overall, I'm happy I did this 30 day run. It got me real world experience with
what is needed to process this music on an on-going basis and what I can expect
if I'm doing daily videos. I'm planning on working methodically with these
changes, as well as taking a small break to handle some bits that have fallen to
the wayside. I hope that in a few months I'll be able to restart daily videos.
