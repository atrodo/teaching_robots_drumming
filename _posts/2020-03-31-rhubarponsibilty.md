---
layout: default
title:  "Rhubarponsibilty"
song: "Responsibility by MxPx"
---

A general problem I've known I've had to deal with soon is handling the fact
that the automatic evaluation, or the fitness function, of how well the neural
networks are performing can only take me so far. The fitness code I have now
does a good job of keeping clearly wrong things from being used in future neural
networks, but it does not do a good job of really evaluating the actual
musicality of the music the neural networks are creating.

This is a critical step to this entire process functioning is the evaluation the
output of the neural networks through a fitness function. At this point all I've
had is code that checks very basic musical properties of the neural network; for
instance, a few things that are evaluated are:

* Check that it's not rapidly changing notes
* That it's not playing notes that are way off-key
* That it follows the melody
* That a drum beat is short and on a beat of some sort

Which does a good job of saying "this doesn't sound like noise" but does very
little to say "this sounds good to the ear". Because investing more into the
fitness code would be a monumental task, I knew that eventually I'd have to get
involve and manually tell the system what's good and what's not.

I'm now at that point and have started creating this manual fitness framework to
include my judgement into the fitness function. I started with the design of how
my code would interact with this new class:

```perl
my $manfit = ManualFitness->new( neural_network => $nn );
my @canidates = $manfit->mutate;
foreach my $can (@canidates)
{
  $can->fitness(rand);
}
my @new_canidates = $manfit->mutate;
```

With a basic idea of how I wanted to work with the code, I set off to write the
class. I pulled out the code from my automated fitness system and started to
rewrite much of it. While I had expected that there was a lot of code
intertwined with the fitness value, I instead found that there wasn't as much as
I had thought. Pretty soon, I had a function that outputted candidate objects
that held these new neural network variations.

After I had that working, I went back to my existing code to start using it.
Since right now my primary target of mutation is a collection of voices that I'm
calling a Band, I started with that. This is when I discovered I had a good
amount of complexity in this design. The new class only dealt with a single
neural network at a time, however I needed to run it for each voice. By itself,
that works fine, however I quickly ran into the problem of coordinating the
neural networks with each other.

I started to rigged up a system that would separate each voice into a correlated
hash for running through the new code, and added the idea of a group to the
candidate class to try and keep them all in sync. The entire point of this
exercise is to test that these new generations make good musical choices; I
needed to make sure that each neural network that I paired together to make a
test sample would continue to be paired together. As I wrote more code to make
sure everything would line up in later steps, the more complexity I discovered.

Then I was starting to write code that would make sure that the lowest
performing group was removed. The trick with that was that the same sets of
neural networks had to be removed from each group at the same time. If they were
not removed correctly, one of the bands would end up without one of the voices
or worse, with only that one voice that had performed well that one time.

What really drove me over the edge and decide that this code was just too
complex was while designing how to store this data in the database. I was trying
to determine the best way to store this hash of correlated data and if I should
make another related table or put it all in a JSON chunk and at that point I
decided that the complexity was growing out of control and needed to be
addressed.

I have an affinity to avoid as much complexity as possible, which I think is
pretty common affinity among programmers. As I've gotten older, I've come to
realize that complexity is inherent in all programs and managing that complexity
is very important. Yes, I could have gotten all my code to be perfect and made
it so that it would never remove items that would invalidate testing. Doing
that, however, doesn't manage my complexity; the code would still be complex,
any updates near it could cause new errors to appear. Worse, I've shifted the
complexity away from the fitness and candidate classes and into the code using
it.

So I started to move some of the complexity into an new candidate class, one
that accepted a hash of neural networks to mutate instead of a single neural
network. This allowed me to push all of the complexity from the code using the
manual fitness class into the candidate class. The candidate code was now more
aware of how to generate variations and this became cleaner, more usable code
all around.

This meant the code that actually ran mutate only had to make one call to
mutate. It would still receive an array back, but each of those items in the
array would contain a hash of each voice's new neural network, easily available.
And the best part about this approach was the data storage, which became as
simple as serializing a single object and everything else would work.

I am quite pleased with how the code turned out, and feel good that I was able
to manage the complexity at this early of a stage instead of suffering with it.
With the journey of the manual fitness complete, I'm now able to create new
samples to test my robots against. The only major piece missing to manually
train these bands is to come up with a way to rank them in a meaningful, 0-100%
kind of way. That, when compared to the rest of this code, seems simpler.
