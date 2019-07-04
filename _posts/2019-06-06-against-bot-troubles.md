---
layout: default
title:  "Against a bot of Troubles"
song: "Against a Sea of Troubles by Five Iron Frenzy"
---

In my last post, I talked about how the fitness function could eventually come
up with a good score to being on the up beat (played half way through a beat).
So, as a fun mental exercise, and I promise it wasn't to prove myself right, I
decided to explain how it could have.

My initial thought was that I was passing the sub-beat number to the neural
network. So it would have to pick up on that input being 0.5. I would imagine,
it would look something like this:

```
  Meaning   Input Node   Weight  Node  Weight  Output Node  Value
  bias:      1.0           1.0           1.0
  on-beat
             0.0   o   \   0.0    o  \   0.0    -   o
                       -   0.0       -   0.0    /   o
                       /   0.0       /   0.0    /   o
                       /   0.0       /   0.0    /   o
                       /   0.0       /   0.0    /   o
  quarter-beat                        
             1.0   o   \   0.0    o  \   0.0    -   o
                       \   0.0       \   0.0    /   o
                       -   0.0       -   0.0    /   o
                       /   0.0       /   0.0    /   o    \
                       /   0.0       /   0.0    /   o     \
  triplet-beat                                              1.0?
             1.0   o   \   0.0    o  \   0.0    -   o     /
                       \   0.0       \   0.0    /   o    /
                       \   0.0       \   0.0    /   o
                       -   0.0       -   0.0    /   o
                       /   0.0       /   0.0    /   o
  sub-beat                            
             0.5   o   \   1.0    o  \   1.0    -   o
                       \   0.0       \   0.0    /   o
                       \   0.0       \   0.0    /   o
                       \   0.0       \   0.0    /   o
                       -   1.0       -   1.0    /   o
```

Obviously this is a stripped down version since my current neural network has 32
inputs, but it has the important features highlighted. To get the upbeat, I have
to trigger on the upbeat when it is 0.5, but not when it's 0.6 or 0.4 or
anything else.

And here is where I made my first pause-and-think. If all the neural network is
doing is multiplication and adding (with a sigmoid function), then how can it
trigger on 0.5 and no other number? Well, including a bias of 1 and some
negative numbers, I would start working out the right combination that could do
it.

Instead, however, I checked for the sub-beat in my neural network inputs, and
it's not there. So I can't use it. But what I can use, maybe you've already seen
it, is that on the up-beat, both quarter-beat and triplet-beat will be on. This
makes a pretty straight forward neural network be able to detect it:

```
  Meaning   Input Node   Weight  Node  Weight  Output Node  Value
  bias       1.0           1.0           1.0
  on-beat
             0.0   o   \   0.0    o  \   0.0    -   o
                       -   0.0       -   0.0    /   o
                       /   0.0       /   0.0    /   o
                       /   0.0       /   0.0    /   o
                       /   0.0       /   0.0    /   o
  quarter-beat                        
             1.0   o   \   0.0    o  \   0.0    -   o
                       \   0.0       \   0.0    /   o
                       -   0.5       -   1.0    /   o
                       /   0.5       /   0.0    /   o    \
                       /   0.0       /   0.0    /   o     \
  triplet-beat                                              1.0
             1.0   o   \   0.0    o  \   0.0    -   o     /
                       \   0.0       \   0.0    /   o    /
                       \   0.0       \   0.0    /   o
                       -   0.0       -   0.0    /   o
                       /   0.0       /   0.0    /   o
  zero                                
             0.0   o   \   0.0    o  \   0.0    -   o
                       \   0.0       \   0.0    /   o
                       \   0.0       \   0.0    /   o
                       \   0.0       \   0.0    /   o
                       -   0.0       -   0.0    /   o
```

And while I didn't fill out the entire matrix, if I just set one line of nodes
to 0.5 on the quarter-beat and triplet-beat, when they are both 1, the output
will also be 1, and the goal is accomplished. The problem is, getting that
particular combination to trigger is a bit of a struggle for the training system
to find. I think that's because of how much of a fine line it is for that to
work. If the inputs are pulled in much over 0.5, then it will trigger on either
the quarter or triplet. Much lower than that and it won't trigger without some
help from the bias or other sources. 

Because of the way that the training works, it's going to be very difficult for
it to hone in on that. Since the number has to be some what precise, over or
under shooting that number will cause the fitness to give it a bad grade because
it will trigger on the wrong beats as well as the right beat. Having a single
value to key off helps the fitness, and therefore the training, tremendously.
