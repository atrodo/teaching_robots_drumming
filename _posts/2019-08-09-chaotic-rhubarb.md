---
layout: default
title:  "Chaotic Rhubarb"
song: "Chaotic Resolve by Plumb"
---

I have been working on adding more infrastructure around the music generation in
order to support diagnosing and improving all the moving parts involved in the
process.  The first things I did was work on visuals. I added code that will
visualize the notes that the melody and each band member plays and turns them
into a video. There's a lot of work there to be done before it's interesting to
watch, but there's enough there to help me examine the output visually.

Once I was able to see what the artists were producing, I started digging into
why they stopped producing sound after about 20 seconds, not to mention that it
sounded like random notes were being played very quickly. That was concerning
because my fitness functions were specifically designed to avoid both of those
things.

Initially I had guessed that some of my earlier code rearrangement ended up
changing the meaning of some fields when the output from the neural net was
converted into notes and lengths. Specifically I had thought that the fields I
use for when to start the note had been changed from what time in seconds to
start at to what beat number to start. This made sense because it would end up
compressing the whole output, effectively speeding it up. However, once I
started to look at the data coming out of the neural network, I realized that
was not what was happening.

So I started questioning if the fitness function was working when the artists
were created. I reversed directions and started writing code that would display
the results of the fitness function for a band. What I found was that most of
the fitness functions were coming back looking good, but not up to the minimum
score I had set for it. So I wrote more code to allow me to rerun the training
to get the numbers higher. In the back of my mind I wondered how the numbers
were lower than the minimum. While I was writing that code and started testing
more, I noticed one time while checking the fitnesses that all of the numbers
were above the minimum. That's when I realized that the fitness function wasn't
returning the same value every time. Luckily, I did know why.

When I was originally designing everything, I wanted to avoid each artist from
being predictable. Naively I decided to add a random number to the neural
network in an attempt to introduce some non-determinism. Unfortunately, that
decision has been nothing but trouble. The goal was that no matter the seed the
neural network would produce an output that passed the fitness function. So I
decided to map how well this was working; I took one of the neural networks and
checked the fitness for every 16-bit seed. Below are the results:

![Fitness for "3yWzKjQp3T1"]({{ "/assets/2019-08-09-chaotic-rhubarb/3yWzKjQp3T1-fitness.png" | relative_url }})

As you can see, the theory completely failed. There are large numbers of seeds
that do not pass the fitness function and a huge range of values that it could
be. At this point I realistically have two options, either put in a lot more
infrastructure and testing to make sure that all seeds pass, or remove the seed
altogether and find other ways to make an artist interesting and not
predictable.

I've decided on the latter and fixed the seed to 12,000 for the time being. This
will allow me work with less variables in play to better facilitate debugging.
With that in place, I got to work on retraining, with predictability, the bands
and artists again. In the end, however, the music did not improve. Turns out,
there are some major deficiencies in some of the fitness functions, but that's a
different blog post for another day.
