---
layout: post
title:  "Teaching Robots how to Drum"
---

I began a journey several months ago to create music through programming. The
proposed process has morphed a lot over that time as my original ideas became
roadblocked. Road blocked either because of computational, technical, or even
resource limitations.  As I'm working on it, I've decided I should chronicle
what I'm doing and what I've learned. So I've decided to start. But, since I'm
in the middle of the process, and I'm lazy, I'm not to back fill the information
I've already learned all at once. However, I will try and revisit those things
as time goes on, I think there's at least a couple things I've learned that is
worth sharing.

To give you an idea of everything so far, here are the current list of features:
1. Implemented a basic Neural Network
2. The Neural Network code has a Perl, C and C/SIMD (AVX2) version
3. I use a genetic algorithm to mutate each generation of the neural network to
find better versions of the neural networks
4. I have started a framework for determining, from a very basic level, if the
Neural Network is producing something musical
5. Using the neural network and a sound font file, I am able to output a
wave-form that uses ffmpeg to produce audio output

As of right now, I have it rigged to produce a clip with a drummer and 2
instruments. I am in the process of adding a new feature that I'm calling the
master-beat. What I discovered after having 3 independently generated audio
tracks was that they were not playing together. The sound wasn't unbearable, but
it wasn't in sync. Originally the thinking was that it would solved with
training, telling all of the different neural networks that what they doing
poorly to correct it. After thinking about it, I realized that was going to be
difficult to do consistently. So instead the plan now is to have a neural
network generate the overall beat or melody of the music, and feed that data
into the other neural networks.

But what about the name? When I was able to generate a drum beat, I generated
two "drummers" and showed them to some friends. One of my friends later told me
that his wife caught him giggling. When asked why he was laughing, he told her
that we were teaching the robots to drum. After a hardy chuckle, I realized it
was a good name for a blog about this journey.
