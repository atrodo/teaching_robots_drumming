---
layout: post
title:  "Tenets of good fitness functions"
alt_title: "Meltdown At Madame Rhubarb's"
song: "Meltdown (At Madame Tussaud's) by Steve Taylor"
---

At the beginning of the year, my original plan was to start uploading new videos
within the first few weeks of January. And indeed I had all of the robots
trained and produced a sample mid-January. The sample was even somewhat
respectable and pleasing enough to listen to. The problem, however, was that
none of the robots were correctly following the melody. That led to 3 more
months of fighting both the fitness functions and the neural network inputs. I
am hoping that I'm nearing the end, again, of having good enough to start. I
finally feel like I've tightened the robot's ability to follow the melody, and
I'm hoping that what comes out will be good enough.

This process has lead me develop some personal best practices, or tenets, when
working on these neural networks and fitness functions. These are still a work
in progress as I'm still exploring them, but this is what I have so far. I'm
also not sure how much they apply to other machine learning, but I have a
suspicion that this applies to things beyond the Genetic Algorithms and Neural
Networks I'm using.

  * Fitness functions need the goal data to exist in the input data
  * Verify that output results is obtainable with input data
  * Input and output data must be of similar type and scale
  * Normalization and denormalization processes must be consistent and lossless
  * Sometimes it's the test input that's a problem
  * Given the chance, fitness functions will find the smallest possible result
  * Requiring a minimum attempt count is better than scoring poor attempts low
  * Fitness functions should be given a linear funnel to obtain values (New, added during [a later revelation]({{ site.baseurl }}{% post_url 2022-12-30-teaching-robots-how-to-drum-2022 %})

As an example, I was attempting to create a new set of fitness functions around
a songs volume or loudness level. The fitness function was suppose to take an
input of loudness level from the melody extraction and score it against the
input loudness, generally as a percentage for each robot so not everything was
as loud as possible. Training the robots ended up taking too many generations
finding a good match, which was my first hint that something wasn't quite right.
Every once in a while it would find a good match, so it was not impossible, just
time consuming. So I began to dig into what was happening.

What I discovered was that the scaling between my input and output was
mismatched; that the input was scaled from -1 to 1 but the fitness function
expected it to be between 0 and 1. One might assume that neural networks can
scale itself and find an appropriate scaling percent to get the desired output.
That is possible, however, the fitness function is going to mark every output
under 0 as bad, reducing the possible range in half. In addition, in order to
produce a smooth range of inputs and outputs, an input of -1 would have to
produce a 0, an input of 0.5 would have to produce 0.75, etc. Said another way,
in order to simply copy the values from input to output, the neural network
would have to add 0.5 to every input and manage half a scale.

In the beginning of testing the loudness function, I found that the robots would
tend to do very well with my test input pattern but not once I applied it to
real melodies. Towards the end I discovered it was because the test input had
negative values, something I realized was insane once I thought about it;
loudness is an absolute value, not relative.

When I was testing the loudness function it would occasionally get a near
perfect score. After investigation would I discover that those near-perfect
scores were because it had discovered that if it only produced 1-6 perfect or
near perfect outputs, it had a great chance of receiving a really good score.
The neural network was originally being punished for not having enough output by
reducing the final score by a calculated value. If it did really well on the
outputs that it did produce it could overcome that punishment. Adjusting the
final score to be exactly -1 when there was not enough output made that problem
disappear.

## Fitness functions need the goal data to exist in the input data

While there are lot claims floating around that Neural Networks can discover
implied information, my experience is a little less of a mystical experience. I
have seem some evidence that neural networks can do that, but in many more cases
I've had far more success with being explicit in the information given. That's
not to say that Neural Networks don't have the capacity to find second or third
order information, but that the information must exist for it to operate on. In
fact, the more explicit the data the better the neural network can use that
data. In the example for example, the neural networks might have found a
loudness level if the loudness was implied by other data, but it is much more
efficient in discovering that data after the loudness was added as an input to
the neural network.

## Verify that output results is obtainable with input data

During testing, I eventually got in the habit of making sure I could get an
output by simulating the neural network just copying the input value as-is to
the output. This allowed me to remove the neural network as a variable and
verify that the fitness function could function as expected given data that was,
by definition, obtainable. This helped to resolve more than a couple bugs by
exposing that the input value I was giving the neural network was either the
wrong in some way, like type, scale, or value.

## Input and output data must be of similar type and scale

In conjunction with data existing and being obtainable, it also of a similar if
not identical type and scale. For instance, if the input is a number 0-100,
mapped to -1..1, the output should also be expected to be a number from 0-100.
This constituted a large portion of of the loudness fitness problem, that the
scales were mismatched.

## Normalization and denormalization processes must be consistent and lossless

In order to get data of a particular format and scale, the output needs to be
encoded in the same manner as the input. There might be some room here for
correlated data types, say an input with 200 discrete values mapping to an
output with 20 discrete values, but not the other way around. This was a
compounding factor in the loudness function issue, specifically the
normalization and denormalization functions were not lossless. Given a single
value that was normalized and then denormalized, the same value was not being
given back. For instance, if 1.0 was normalized but became 0.98 after being
denormalized, that's problematic. This case was a bit more extreme, more like
giving -0.25 and getting back 0.4.

## Sometimes it's the test input that's a problem

Using test data when testing can help eliminate variables that are hard to
control for. However, care must be taken because that test input can be the
problem. A couple times I discovered that what I thought was good input ended up
not representing either the type, scale, or values. Generated data that I
thought represented a testable aspect of input data could cause me to spin my
wheels trying to figure out why the robots just can't hone onto a good fitness.
I have spent a lot of time fiddling with the fitness function and output
processing trying to resolve the issue, only to realize that my test data didn't
represent what I wanted to test.

## Given the chance, fitness functions will find the smallest possible result

The couple times that this one came up was rather entertaining. I would see a
few runs come back with a 100% fitness score, which immediately raised red
flags. Each time after digging into the data, I discover that the neural network
had discovered that if it gives 1 perfect output, it can overcome the penalty of
not have enough output. So most if not all fitness functions should know what a
good amount of output should be and require it. Anything that fails to produce
sufficient amounts of output should cause the fitness function to stop checking
immediately.

## Requiring a minimum attempt count is better than scoring poor attempts low

A related lesson to the above is that punishing an output for too little output
is not as good as just giving it a flat, terrible score. In one case, I was
punishing an output such that if only gave say half the expected output with a
-.5 for each missing entry. In the case of it missing 50% of the output, but the
50% that was there was really good, it could still score 40%. This might not
seem like a big deal at first, but it really becomes evident when the starts of
a better output can't compete because its first attempt got all the items right
but was like 30%. In short, it starves good networks with known-to-never be good
networks. Fixing it to a static score means it won't even have a chance. It
wasn't evaluated on its merits, only that it demonstrated the ability to take
advantage of the system it was given.

---

As I work towards finishing things, I'm trying to get through the bits. I've
stopped working on the fitness functions as they work for the most part. They
won't be great, I know a few areas that need more investigation, and I have a
few ideas to make it better. I am hoping to generate a week at a time, improve,
generate another week. Ideally that would be a week of uploads every week, but
may have to settle for spreading them out. Unless things are really bad, in
which case I may take another year to struggle through more of this.
