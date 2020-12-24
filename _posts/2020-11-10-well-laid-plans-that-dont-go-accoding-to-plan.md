---
layout: post
title:  "Well Laid Plans That Don't Go According to Plan"
alt_title: "A Robot Hope"
song: "A New Hope by Five Iron Frenzy"
---

While I'd like to think that this project is going well, the unfortunate reality
is that it could be going much better. We don't always want to talk about how
things didn't go according to plan, especially when no one knew there was even a
plan at all. That said, I feel like I should write down about one of these well
laid plans that no one knew about and how it didn't go as planned.

It has been my goal for some time to get to the point where I can set the robots
free and allow them to make music for the world to hear. The has been long and
has been much more time consuming than I wanted it to be. Some of it, I admit,
is me trying to do things a certain way and learn really what is going on. The
other part is just not realizing how deep the shallow end actually is; that my
simple solutions to the problem turned out much larger than I anticipated. In
the end however, I'm okay with my current situation and fairly comfortable of
these realities as they generally happen to some degree on most software
projects.

In late September, I had decided to make a deadline and had decided on it being
early October. I made a list of things that needed done before going live, and
the list was fairly short and reasonable; I needed a way to show each robot
"think", colorize each robot uniquely, add a more visually interesting
background, and finally upload the whole thing to YouTube once created. So I
started down the list, quickly, and I think I did a good job of not getting
bogged down in the details. For example, when I worked on the "brain images" of
the robots, I decided not to figure out how to dynamically shape and draw all
the lines and curves. Instead I opted to draw several of them onto a sprite
sheet and randomized which curves to draw onto a template.

What ended up taking more time, however, was the visualizations. I had initially
tried to generate something with 2D tools I had but quickly ran into limitations
with those tools. So after some digging, I discovered that the old `WinAmp`
visualizations I was hoping for was produced by a piece of software named
[`MilkDrop`](http://www.geisswerks.com/about_milkdrop.html). I was then happy to
discover that there is an open source reimplementation of `MilkDrop` called
[`projectM`](https://github.com/projectM-visualizer/projectm/), so I decided to
integrate `projectM` into my code to fill in that visualization gap.

It was a struggle, however, to get `projectM` working and took a fair amount of
time to get right. I had to compile [`OSMesa`/Off-Screen
Mesa](https://docs.mesa3d.org/osmesa.html) manually since I'm running this on a
server with no GPU. Then I had to figure out the proper configuration and
initialization of all the bits and pieces to get `OSMesa` and `projectM` working
together nicely. Even though it took longer than I wanted, I was eventually able
to get `projectM` to work after some pretty minor modifications to it for my
needs.

Thanks to getting `projectM` working, I was finally showing some nice
visualizations. By the time this was working however I was already past my
initial deadline. That was okay, by this point I was moving the deadline daily
since I was making progress and I had an idea of what needed to happen. I had
already worked on a general schedule of what songs the robots would play and how
I would structure the process. I wanted at least a week of the robots playing a
song each day before they started playing Halloween music. That meant I needed
to be done at least two weeks before Halloween to make that happen.

Generally, at this point, you'd think that not making the goal would be the part
that went wrong. That, unfortunately, was not the part that went wrong. I was at
the finish line, I had the visualizations, and I liked how they looked. I had
what songs the robots would play all figured out and while I didn't have the
YouTube upload ready, that bit wasn't critical. No, the part that went wrong was
how it sounded; the sounds and the must the robots were making still sounded bad
once I really sat down and listened to what was being produced. Not all of it
was bad, a lot of parts were just fine, but some parts were not fine and it was
noticeable. If my goal is to get people to listen to an entire song and not
avoid listening again, this was not going to work.

That made me have to make a hard decision: I was not going to rush into the
Halloween schedule. I didn't make the decision immediately, I took a couple days
to see if it was something simple and obvious, like another missing [Circle of
Fifths]({{ site.baseurl }}{% post_url 2020-08-25-progress-update %})) error.
Once I started to dig in however, I knew it wasn't simple; I didn't have a
couple of days of work to fix the sound, I had at least a couple weeks of work.

That meant that I now had to decide when the next deadline was going to be.
Missing Halloween meant that it had to be at least sometime in November when I
could try again. Instead, however, I decided not to do attempt to schedule a new
deadline before the end of the year.

This decision was made as a combination of two factors: first, I was pushing
myself past my normal pace for about four weeks, a pace that was not sustainable
for much longer, especially with the holiday season right around the corner.
Second I know that the robots are going to be in rough shape the first go
around; I am expecting the robots to most likely only sound tolerable. However
if they are merely tolerable then I have a strong feeling that people will be
extra critical of their flaws when they compare them to known Christmas music
and I'm not sure the robots would be able to recover. I wanted those extra 6-8
weeks in October and November to focus on tweaking these robots to be better
than just tolerable before letting them play Christmas music.

It's been a few weeks since I made that decision, and I think I made the right
decision to move it to next year. I'm feeling pretty good about it after having
some time to think about it. There is still about two months left before my new
deadline and I definitely needed the time. I'm starting to get close to getting
the robots up to my currently high standard of tolerable.
