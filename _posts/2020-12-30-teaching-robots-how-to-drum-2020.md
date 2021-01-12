---
layout: post
title:  "2020: Teaching Robots how to Drum"
alt_title: "Good Robot Wenceslas"
song: "Good King Wenceslas by Relient K"
---

It's the end of the year, and I thought I'd take a moment once again to reflect
on Teaching Robots again. While it has been a bizarre year, I'm taking a moment
to look back at what I've done in the past year and how I feel about it. I must
admit that today, looking back at what I've done I feel proud about what I've
been able to accomplish this year, despite the set backs the year had to offer.

Since it feels like a good tradition to keep, I've decided to once again compile
a list of what I did accomplish this year, just like I did last year.

* Made around 100 commits
* Made 8 blog posts
* Rebuilt how I store and process musical data
* Improved the [melody extraction process]({{ site.baseurl }}{% post_url 2020-02-20-improving-melody-extraction %}})
* Improved the infrastructure for my scripts
* Added a [manual fitness process]({{ site.baseurl }}{% post_url 2020-03-31-design-manual-fitness-function %}})
* Derive a way to [make colors from strings]({{ site.baseurl }}{% post_url 2020-04-15-making-colors-out-of-strings %}})
* Made the movement of data in and out of the database more efficient
* Did a fair amount of work inside [`ffmpeg`]({{ site.baseurl }}{% post_url 2020-05-13-ffmpeg-libtheora-from-slow-faster-encoding %}}) solving my personal [`ogg` issues]({{ site.baseurl }}{% post_url 2020-05-14-ffmpeg-libtheora-multistream-ogg-chaining %}})
* Converted my old fitness function code to live in the same space as the newer manual fitness code
* Discovered and [gushed over `Essentia`]({{ site.baseurl }}{% post_url 2020-09-10-building-new-melody-extraction-using-essentia %}})
* Converted my melody extraction process to `Essentia`

Overall that list is pretty encouraging; I've accomplished a good deal of
progress towards my final goal of putting the Robot's music out there for the
world to listen to. The list of items is a bit shorter than it was last year,
but I think that can be attributed to the fact that I was working on a couple
larger things. Plus, ya know, pandemic.

With this year wrapping up, I have to say that I'm ending on a high note. After
the difficulties I had once I sat down and listened critically of the songs that
were being made, I made the decision that it just wasn't good enough. So I began
the debugging process of determining why it didn't sound good. I retraced my
steps, manually checking that the right notes were being played as expected.
That however was a dead end because all of the notes were playing as expected.
So I started to listen to groups of instruments together thinking that some
of the instruments were not sounding good together. After several combinations,
I discovered that a single instrument was sounding pretty terrible, even when
played by itself.

So I decided to take the quick fix and swap the instrument out for another one,
and that worked. I worked on some more investigation, after which it was clear
that one of the percussion instruments was now a problem and sounded wrong. That
swap was going to be harder to do, I don't have as many percussion options. I
decided to start looking for another solution.

As part of the initial phase of this project, I decided to use `SoundFont` to
generate the instrument audio. Back then, I had done some quick research to
determined that there wasn't a Perl library that would be able to synthesize
from a `SoundFont` for me, and using `FluidSynth` didn't seem reasonable since
it didn't look to have an easy to access API. What I did find was a [`SoundFont`
Perl library](https://metacpan.org/pod/MIDI::SoundFont) that could read the file
and give me all of the data from a `SoundFont`. So while I knew it wouldn't be
great, I decided to take a stab at using that data to synthesize it myself,
building a makeshift synthesizer knowing it would be good enough for the time
being.

And at the time, it did was good enough. But at this point I've decided that
it's time to find another solution. I started to look for more synthesizer
libraries and came across
[`TinySoundFont`](https://github.com/schellingb/TinySoundFont), a "SoundFont2
synthesizer library in a single C/C++ file". This looked perfect to me because
it would be easy to include a single C header with `Inline::C` and use it.

Instead of using `Inline::C`, I decided make a Perl module around it. I'm
working now on fleshing out the entire API, but I've already been able to run
some comparisons and both intruments that were causing issues are easily much
better sounding no. I'm really excited about finishing up the module and
integrating it with the rest of the project.

All that's left is the bug that makes a negative volume cause an exponential
audio experience.
