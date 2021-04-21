---
layout: post
title:  "Launching Robots Learning to Play the Drums"
alt_title: "To Rhubarb a Fire"
song: "To Start a Fire by Five Iron Frenzy"
---

Over the past few months, I've been steadily working towards releasing what I
have taught these Robots. I am pleased to announce, that as of today, I've
successfully done [exactly
that](http://www.youtube.com/channel/UC4ptA0k6_cSVTOYVOtBTQXQ). I will be
releasing a new song, every day, for the next thirty days to showcase what I've
taught these robots so far. After that, I am going to evaluate what needs to
happen, but right now I'm already excited about the future.

Since [the beginning of the year]({{ site.baseurl }}{% post_url
2021-01-28-discovering-microcosms-between-beats %}}) I've been doing working
hard at this milestone. I wouldn't say its been smooth sailing, because it has
not been. I tried for several weeks to refine the melody extraction process.
While I was able to create some tools to help analyze what's happening, I was
not able to turn that into progress. The robot ears still struggle to hear the
most prominent parts of a song the way a human does.

One thing I did notice is that it did better with instrumental music than with
more pop or rock music. That's when I had the thought and decision to switch
from more contemporary songs from recent decades past to classical music. Its
not perfect, but the extraction does work better.

I switched gears and started to build a go forward plan, making a list of my
minimum required items. The biggest was finalizing the visualizations. I faced
two major hurdles; first getting the visualizations to look right and second to
get them done in a timely manner. I had already started a good base getting
[`projectM`](https://github.com/projectM-visualizer/projectm/) and
[`OSMesa`/Off-Screen Mesa](https://docs.mesa3d.org/osmesa.html) working
together, but because LLVMPipe, the JIT version of `OSMesa`, was not working
correctly on my machine, I had to reorganize or else be faced with extreme
rendering times.

Eventually, I took everything I had gotten working and wrote
[Video::ProjectM::Render](https://github.com/atrodo/Video-ProjectM-Render),
which is not ready for [CPAN](https://metacpan.org/) but is usable for my
purposes. I plan on writing a whole blog about `Video::ProjectM::Render`. But
that allowed me to generate most of the content on my current machine, but
render the visualization on another machine. That cut my render down to be
limited by network bandwidth. It is not real-time, but for the final creation
of the videos, I am okay with it taking longer. What I was not accepting of was
waiting 6-10 hours for it to render one video.

So that means I've officially stepped into the next phase of my project of
Teaching Robots to Drum, and I still have quite the list to do. Hopefully
getting some real world understanding of how the Robots perform on a wide range
of songs will lead to some new adventures on making some wonderful music.
