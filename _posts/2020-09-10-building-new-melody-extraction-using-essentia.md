---
layout: post
title:  "Building new Melody Extraction using Essentia"
alt_title: "We're half way there"
song: "Livin' On A Prayer by Bon Jovi"
---

A new bee has entered my bonnet. Actually, it's an old bee: melody extraction.
As I am working towards generating music, I wanted to play with more ideas to
improve melody extraction. Unfortunately none of these ideas panned out, so I
went looking for new ideas and came across the
[`Essentia`](http://essentia.upf.edu/) library. I am super excited about finding
this library and being able to use it.

I had several ideas, mostly centered around improving the duration of the notes
I did find. These unfortunately all fell flat, producing nothing better than I
previously had. It turned out that the issue was less about note duration and
more about which notes to choose. The code does reasonably well with simple
musical sequences, but as more complex sounds are introduced, as is found in
most music as you add instruments, it becomes harder to find and focus on the
right note.

After those failed ideas, I started down the path of both thinking of new
possible incremental improvements and trying to find new techniques by skimming
[`MIREX`](https://www.music-ir.org/mirex/wiki/MIREX_HOME). `MIRIX` is "Music
Information Retrieval Evaluation eXchange" and is an annual contest that "is to
compare state-of-the-art algorithms and systems relevant for Music Information
Retrieval". In other words, `MIRIX` is for promoting and improving the field of
"Music Information Retrieval", melody extraction being a part of that. That
makes it a perfect resource for what I have been working on.

In my initial search for the basics on how to go about extracting melody from
music, I had found
[`MELODIA`](https://www.justinsalamon.com/melody-extraction.html). Researching
it is what originally lead me to `MIREX`, so I already knew of the existence of
this yearly contest. At the time I hadn't dug in deeper because `MIREX` doesn't
have a public code requirement, so while I could see the results and high level
descriptions, there was not a lot of concrete details on implementation.

This time around, however, I was looking more for ideas on how the best
performers did it than code since I already had some basic, working code. So I
decided to choose a category and start looking at all the entries that did well
and read the abstracts. I started with 2010 and moved forward. I found that the
past few years, most of the entires were done with neural networks, which I'm
not too sure of yet but have them in the back of my mind to revisit at a later
melody extraction dive.

I started to notice some of the same names repeat between years. One name I
had noticed come up a couple times with various co-authors was Emilia G ́omez.
I initially recognized the name because Emilia was the co-author of the above
mentioned `MELODIA` with Justin Salamon. In 2016, she worked with Juan J. Bosch
to produce a submission that performed well, so I made sure to read it.

For the submissions in 2016, Juan J. Bosch and Emilia G ́omez worked together on
a submission that `MIREX` called
[BG1](https://www.music-ir.org/mirex//abstracts/2016/BG1.pdf). While reading the
submission, two things stood out to me. First, this appeared to be built upon of
the `MELODIA` work. Second, there was a [GitHub
link](https://github.com/juanjobosch/SourceFilterContoursMelody) that took me to
the code for this submission.

At this point, I got really excited. This was great, I finally had an
opportunity to see another code base that does melody extraction. So I looked at
the repository and discovered that under the hood it uses
[`Essentia`](http://essentia.upf.edu/) to do all of the heavy lifting. So with
my curiosity piqued, I went and started investigating `Essentia`, and what I
discovered was a cornucopia of music and sound tools, including an
implementation of `MELODIA`.  So I took a couple weeks and started to readjust
my melody extraction to emulate `MELODIA` using `Essentia`.

The results have been really good, I think its note estimations are much better
than my initial version. However, I think some of the octave selection isn't as
great and that seems to negatively affect the output.  That said, I feel like
using `Essentia` has been a great improvement to the process. I have some more
experiments planned because we're not quite there, but it's absolutely a better
foundation to work with than I had before.

I have found working with `Essentia` to be a breeze and an overall pleasant
experience. It is more of a library of simple and complex tools, and the API
isn't fancy or clever. What it does end up doing instead is being very straight
forward: you allocate your steps, you configure the inputs, parameters and
outputs, then you run `compute()`. The creators could have done a lot of
meta-magic and made everything work with more magic, but they didn't and I think
it's better for that. It's extremely regular and friendly to when I add a new
item to the pipeline. The documentation is good, and when it's lacking a little,
the code is almost always straight forward enough to crack open and follow.

Changing to `Essentia` was not without its difficulties. It's a `C++` library, a
language I have little more than a passing knowledge of, and have probably
tripled my knowledge of it through this process. To use it, I used
[`Inline::Cpp`](https://metacpan.org/pod/distribution/Inline-CPP/lib/Inline/CPP.pod)
which was very nice to have. I have used
[`Inline::C`](https://metacpan.org/pod/distribution/Inline-C/lib/Inline/C.pod)
in the past to do some optimizations, but this was the first I had used an
`Inline` module to interact with a library. Luckily, the biggest pain point,
was this error that I got when I would `#include <essentia>`:

`random.h:269:17: error: macro "seed" passed 1 arguments, but takes just 0`

After a good bit of research, I discovered that the user-facing headers of
`perl` redefines several C pieces to something that is likely more useful to an
XS programmer using macros. In some cases, like this one, including the `perl`
headers before a standard library causes errors; it redefines `seed()` to use
the `perl` version but then `libc++` is unable to use its internal version in
its headers.  I found out that it's generally best practice to `#include
"perl.h"` and the like after all your standard includes.

However with `Inline::C`/`Inline::Cpp`, since it includes the `perl` headers for
you, there's not much opportunity to do this. I really struggled to get it to
work because the
[`auto_include`](https://metacpan.org/pod/distribution/Inline-CPP/lib/Inline/CPP.pod#auto_include)
feature looks like it should be usable to solve the issue, but it just doesn't
seem to allow you to override or remove the auto-inlcuded `perl` headers
completely. Finally I found a `p5p` posting that lead me to the solution of
`#undef seed` to fix the issue. Less than ideal, and took me much too long, but
it works.

Overall using `Essentia` is a major improvement over my initial attempts at
melody extraction. I feel like I can achieve a much higher success rate going
forward now that I'm using it, and hopefully this can keep the melody extraction
bee out of my bonnet for a while.
