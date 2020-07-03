---
layout: post
title: "The Neural Network Convergence Problem"
alt_title:  "Some Kind of Robot"
song: "Some Kind of Zombie by Audio Adrenaline"
---

While working through adding the "new band" command I noticed that the drummer
was struggling to generate a new instrument I had added. After a some
investigation, I discovered that it had broken on the "up-beat" fitness I had
just created. Originally, for the drummer, I had an "on-beat" fitness function
to make sure that the output was a quick, on the down beat activation. I added
several new fitness functions, including one for on the downbeat, on each
quarter beat, and each triplet beat. At the same time, I also added one for the
up-beat. This one happens exactly between each down beat, so when divided into
12ths, this is suppose to activate on the 6th (when starting at 1) part.

The problem was that the fitness function wasn't converging to fitness. It was
struggling to find the up-beat. Which was abnormal, because the other fitness
functions were not struggling to reach fitness. While I think that eventually it
would have found a fit version, it dawned at me that there were no inputs that
directly related to the upbeat, but there was an input for each of the other
types of beats. I modified the down-beat input so that -1 would represent an
upbeat and 1 would represent a down-beat, and almost like magic, it quickly
reached fitness.

For a while now, in the back of my head, I've had this feeling that this neural
network approach would end up having a difficult time being as dynamic as I want
it to be. Part of this thought process has involved thinking about how neural
networks can be used to do conceptualize the things that brains can do. So for a
couple weeks now I've been applying neural networks to everyday things, like
likes and dislikes, math, memorization, etc. And with a lot of hand-waving, I
was able to "come up" with an explanation how a neural network could do those
things.

However, I started to run into trouble when I started applying neural network
thinking to emotions. It was rather unnerving when I could explain the ways
people were feeling as a particular set of learned reactions in the neural
network. While I could convince myself that a particular reaction could be
modeled in a neural network, it was harder to convince myself that was actually
what was happening.

In reality, I suspected that they can't actually be applied to the things I had
been applying to them. Math was always the one that really stuck out to me as
difficult for a neural network to handle. While one might argue that a lot of
basic math is based on memorization, a lot of math is not. When I see a math
problem like ```1 * ( x / ( 1 + abs(x) ) ) when x = 2```, I haven't memorized
that problem. There's a mental process that steps through that problem (and part
of that process involves reaching for a calculator). Sure one might argue that
the neural networks work in concert together to notice all the patterns and
produce an answer, but it still bothers me that the process doesn't feel like it
fits a neural network model, especially the learning aspect.

I think this is where people can go wrong, applying almost magical properties to
neural networks. You can explain away these problems with the "sufficiently
advanced complier" fallacy, hand-waving the complexities of the problem with
more inputs and deeper neural networks. I must admit, seeing a neural network
detect something unexpectedly feels a lot like magic.

Because of this little hiccup, and a lot of thinking about the problem, I now
think that neural networks are really just nice mathematical models that do a
good job of learning feature detection. They cannot divine information that
wasn't there, which is why giving it more and better modeled data is vital to a
successful neural network. The more magical things that they are doing, like
with convolutional neural network, are not sophisticated because of the
neural network, but because of the domain knowledge and research that has gone
into right way to prepare data for the neural network to extract the desired
information.

This doesn't mean I'm going to rip everything out right away. What it does mean
is that I'm going to have to explore how to overcome this limitation. I'm hoping
it'll be a fun problem to solve.
