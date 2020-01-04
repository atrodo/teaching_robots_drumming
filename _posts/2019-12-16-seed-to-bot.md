---
layout: default
title:  "Seed to Bot"
song: "Seed to Sow by Michael W. Smith"
---

This is part 1 of a 2 part series about Discrete and Fast Fourier
Transformations. The first part is about the theory and how DFTs work while the
second part is about the practical application and code execution to get the
frequency data from a sound sample.

After finally being able to get the fitness functions from my last post to
behave into something musical, but not quite entirely satisfactory, I began
working on melody extraction from music. The hope is that I can use this as a
way to have some starting melodies to generate music from. I haven't gotten very
far, and I'm aware of the fact that there is quite a bit of active research into
this subject. I do not believe that the technique I am working on will be able
to compete with the research being done by much more capable professionals.
However, the plan isn't to be the best melody extraction tool. Instead the goal
is to get good enough melody extraction that I'm able to use it for song
generation. I'm estimating that somewhere around 60%-0% accuracy will be
sufficient for my purposes, which from a casual look at the field seems
obtainable.

Knowing what I know about music, and oddly, audio/visual compression, I knew
that DFT, FFT or DCT was the thing that I wanted. I always knew that this class
of algorithms took a signal and extracted the frequencies out of them. What was
always unclear is how they worked. That launched me on a few month journey of
discovery. I am going to attempt to share that journey with you here.

<script async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML"></script>

## Prologue

I must admit, I have a hard time reading math notation. It's not that I can't, I
eventually recall my math schooling. It's more that I can't readily translate it
to what it means in terms of programming concepts. My brain doesn't connect math
and programming very well, and it spends most of its time in programming.

<div>
$$
\begin{align}
X_k &= \sum_{n=0}^{N-1} x_n \cdot e^{-\frac {i 2\pi}{N}kn}
\end{align}
$$
</div>

This means that when I see things like the above on Wikipedia or StackOverflow,
my brains says:

> Okay, so what code do I write to implement that? I can make a for-loop, and
> maybe I need to do two for-loops? But why is there an `e` in an equations
> about frequencies?

So I'll read or look around some more, and perhaps I'll see the below formula instead:

<div>
$$
\begin{align}
X_k &= \sum_{n=0}^{N-1} x_n \cdot \left[\cos\left(\frac{2 \pi}{N}kn\right) - i \cdot \sin\left(\frac{2 \pi}{N}kn\right)\right]
\end{align}
$$
</div>

Which seems somewhat more reasonable since there is a \\(sin\\) and \\(cos\\)
included, but it doesn't really mean much to me, especially the mechanics of it.
Because that's really what I'm most interested in, the mechanics and the
concept. First, the mechanics of what code I can write to make it run, and
second the concept so I can understand what my code is doing.

Ironically, the more I look at those two formulas, the more I am putting the
pieces together. However, that's because I've spent a fair amount of time
understanding what's going on and am able to apply my new found knowledge of
Discrete Fourier Transformation to the equation. I can assure you, that formula
was no help to me in understanding Discrete Fourier Transforms.

## Discrete Fourier Transformation (DFT)

[Intuitive Understanding of the Fourier Transform and FFTs](https://www.youtube.com/watch?v=FjmwwDHT98c)

This video was formative for me in understanding the concept of DFT. So much so
that I don't think I can do a better job than William does in that video. I
highly suggest you watch that video before continuing.

If you didn't watch the video, I will attempt to summarize the important bits
here so you have some baseline information about what I will be talking about in
the next sections.

Imagine, if you would, the waveform of a sampled sound as a bendable string. You
can curl it up into a circle without distorting the data points themselves, each
point always stays the same distance from the center. Then you could curl it
tighter or looser around this circle. As you do this, you change which point
overlaps the first point in the string. This periodic nature is a frequency,
and we can calculate the exact frequency in hertz with more information later.

As the data is moving around this circle, preserving each point's length from
the center, we can start adding all of these point's coordinates, their \\(x\\)
and \\(y\\) position, together. Because of the periodic nature of this circle,
we end up moving the on-going sum of these two coordinates between positive and
negative numbers. But because the data doesn't always sum to 0, the final
coordinate summation for a frequency will be somewhere on the graph, possibly
not between the extremes of our input data. You could think of this final
\\(x\\) and \\(y\\) coordinates as the center of gravity for the frequency.

What this ends up doing is allows the points that contribute to a specific
frequency to align on the graph, so all the peaks of the waveform are pointed in
one direction and all the valleys are in the opposite direction. This moves the
center of gravity heavily in the direction towards the peak of that particular
frequency. This allows us to measure the strength of each frequency relative to
each other frequency regardless of the phase (where in the wave form the
sampling began) of the original signal.

## Cooley-Tukey Fast Fourier Transformation

Unfortunately, even with this excellent video, I was at a loss of how
Fast-Fourier-Transformation works. I knew the [algorithm is named after Tukey and Cooley from their groundbreaking work in 1965](https://en.wikipedia.org/wiki/Cooley%E2%80%93Tukey_FFT_algorithm).
I also found [this
article](https://jakevdp.github.io/blog/2013/08/28/understanding-the-fft/) which
helped get me going in the right direction. But I thought I could do a better
job explaining the theory behind FFT.

While the naive DFT case does something very straight-forward, that is it adds
each value multiplied by its angle in sequence, Tukey-Cooley doesn't.
Tukey-Cooley takes seemingly unrelated values and adds some \\(sine\\) and
\\(cosine\\) values, randomly throws some additions and subtractions in there
and out comes correct numbers. It feels like magic, even if you do know how
Discrete Fourier Transformations work.

Since each point in the waveform is calculated at a specific angle, we can
express each point with an angle. So, the first point in the waveform is always
at 0°. The second point is calculated at \\(\\frac{1}{BinCount} *
OutputFrequency°\\); for instance when \\(BinCount\\) = 8, the points are at 0°,
45°, 90°, etc for the first output frequency, 0°, 90°, 180° etc for the second
output frequency, and so on.

One of the interesting properties of this is that when \\(BinCount\\) is even
then \\(BinCount/2\\) is either 0° or 180° for every output frequency. The
simple case is easy to checked; for the output frequency of 1, which is 1 time
around the circle, halfway around the circle is 180°. Each other output
frequency multiplies that angle by how many times around the circle it needs to
go; output frequency of 2 would be \\(180° * 2 = 360°\\), output frequency of 3
would be \\(180° * 3 = 540°\\). Reducing these to the circle yields either 0° or
180°. Put another way, \\(\\frac{1}{2} * OutputFrequency\\) is always an
integer plus \\(\\frac{0}{2}\\) or \\(\\frac{1}{2}\\); with the even output
frequencies in the former, odd output frequencies in the latter.

Then less obvious extension to this property when \\(BinCount\\) is even is that
\\(i + \\frac{BinCount}{2}\\) is always either 0° or 180° offset from the angle
of \\(i\\).  And even less obvious is that \\(i + \\frac{BinCount}{4}\\) is
always either 0°, 90°, 180° or 270° offset from \\(i\\). And we can determine
which angle it is by using the modulus of 4 in the same fashion that we used the
even and odd rule in the \\(\\frac{BinCount}{2}\\) case. For instance, when
\\(i\\pmod 4 = 1\\), then we use 90°, and 270° is used when
\\(i\\pmod 4 = 3\\).

This pattern continues as long as \\(BinCount\\) can be divided by two. What
that enables us to do is build up the final value of each output frequency in
piecemeal by calculating values based on relative angles to each other until we
have the final answers. What ends up happening is that we do the same summation
as the DFT, but we do it by generating successive components that are at the
correct angle relative to the existing summation.

We can achieve this rotation of each piecemeal summation through the magic of
the [Twiddle Factor](https://en.wikipedia.org/wiki/Twiddle_factor), which in
this case is the
"[Root-of-Unity Complex Multiplicative Constants](https://en.wikipedia.org/wiki/Root_of_unity)".
Although I do not understand how precisely this magic works, what I do know is
that it transforms a complex number angle without altering its magnitude. In
laymen terms, it rotates the \\(x\\) and \\(y\\) coordinates of a point around a
unit circle by a fixed degree rotation such that the length of the line remains
constant but the angle changes. Take for example \\(x=1, y=0\\), if we use an
angle of 180°, that would change it to \\(x=-1, y=0\\), or 315° would yield \\(x
= \\frac{1}{\\sqrt{2}}\\), \\(y = \\frac{-1}{\\sqrt{2}}\\). The length of the
line remains the same, 1, while only the angle changes.

I like to think about it like having a paper with each of them having one of the
input values marked as a dot at 0° and numbered based on the output frequency
they will represent. After each iteration you turn the paper a specific amount
based on the round and the paper number. You copy the dots from another piece,
again based on round and paper number. By the end you have the waveform laid out
in the circle.

## FFT Example

Let's walk through an example using an 8 item DFT.  In the below chart, the
numbers at the top are the input values and the numbers on the left are the
output frequencies. Each cell is the angle at which the input value is for that
output frequency.

We start by filling the initial values all at 0° into very specific rows. The
ordering of the inputs may seem a little bit peculiar at first, but its required
and I explain more about it in the Bit Reversing section below.

                                      360° 
                            0    1   2   3   4   5   6   7  
                     r00:   0°                              
                     r01:                    0°             
                     r02:            0°                     
                     r03:                            0°     
                     r04:        0°                         
                     r05:                        0°         
                     r06:                0°                 
                     r07:                                0° 

For the first round, we split the entire output frequency list into 4
(\\(\\frac{BinCount}{2}\\)) groups of 2. We then add both inputs in each group
together, at 0° for the even output and the odd input at 180° for the odd output
for each group.

                                                            180° 
                           0    1    2    3    4    5    6    7 
    r10=r00 + r01 @   0°:  0°                  0°             
    r11=r00 + r01 @ 180°:  0°                180°             
    r12=r02 + r03 @   0°:             0°                 0°     
    r13=r02 + r03 @ 180°:             0°               180°     
    r14=r04 + r05 @   0°:       0°                  0°         
    r15=r04 + r05 @ 180°:       0°                180°         
    r16=r06 + r07 @   0°:                 0°                  0° 
    r17=r06 + r07 @ 180°:                 0°                180° 

In the second round, we split the output frequency list into 2
(\\(\\frac{BinCount}{4}\\)) groups of 4. We set \\(i\\) to \\(i\\) plus
\\(i+2\\), but we rotate \\(i+2\\) either 0° when \\(i\\pmod 2 = 0\\) or 90°
when \\(i\\pod 2 = 1\\). Then we set \\(i+2\\) to \\(i\\) plus \\(i+2\\) after
we rotate \\(i+2\\) either 180° when \\(i\\pmod 2 = 0\\) or 270° when
\\(i\\pmod 2 = 1\\). Once we do that, we have this interesting checker pattern
and you can see all the values missing from the first half are in the second
half and vise-versa.

                                                             90°
                           0    1    2    3    4    5    6    7 
    r20=r10 + r12 @   0°:  0°        0°        0°        0°     
    r21=r11 + r13 @  90°:  0°       90°      180°      270°     
    r22=r10 + r12 @ 180°:  0°      180°        0°      180°     
    r23=r11 + r13 @ 270°:  0°      270°      180°       90°     
    r24=r14 + r16 @   0°:       0°        0°        0°        0° 
    r25=r15 + r17 @  90°:       0°       90°      180°      270° 
    r26=r14 + r16 @ 180°:       0°      180°        0°      180° 
    r27=r15 + r17 @ 270°:       0°      270°      180°       90° 

So for our final round, we will finally weave all the values into their final
angle and values. This time we have 1 group of 8 and our angles are in
increments of 45°. So for output frequencies 0-3, we rotate \\(i+4\\) to 0°,
45°, 90° and 135° respectfully when adding it to \\(i\\). For output frequencies
4-7, we use 180°, 225°, 270°, and 315° instead.

                                                       45°
                           0    1    2    3    4    5    6    7 
    r30=r20 + r24 @   0°:  0°   0°   0°   0°   0°   0°   0°   0°  
    r30=r21 + r25 @  45°:  0°  45°  90° 135° 180° 225° 270° 315°  
    r30=r22 + r26 @  90°:  0°  90° 180° 270°   0°  90° 180° 270°  
    r30=r23 + r27 @ 135°:  0° 135° 270°  45° 180° 315°  90° 225°  
    r30=r20 + r24 @ 180°:  0° 180°   0° 180°   0° 180°   0° 180°  
    r30=r21 + r25 @ 225°:  0° 225°  90° 315° 180°  45° 270° 135°  
    r30=r22 + r26 @ 270°:  0° 270° 180°  90°   0° 270° 180°  90°  
    r30=r23 + r27 @ 315°:  0° 315° 270° 225° 180° 135°  90°  45°  

At this point, we have the final output, each input mapped at the correct angle
and combined to create the final output value.

    Group 1:       Round 1           Round 2                Round 3
      000 - r00 \_/ r00+r04 = r10 \   / r10+r12 = r20 \         / r20+r24
      100 - r04 / \ r04+r00 = r11  \ /  r11+r13 = r21  \       /  r21+r25
    Group 2:                        -                   \     /   
      010 - r02 \_/ r02+r06 = r12  / \  r12+r10 = r22    \   /    r22+r26
      110 - r06 / \ r06+r02 = r13 /   \ r13+r11 = r23     \ /     r23+r27
    Group 3:                                               -
      001 - r01 \_/ r01+r05 = r14 \   / r14+r16 = r24     / \     r24+r20
      101 - r05 / \ r05+r01 = r15  \ /  r15+r17 = r25    /   \    r25+r21
    Group 4:                        -                   /     \
      011 - r03 \_/ r03+r07 = r16  / \  r16+r14 = r26  /       \  r26+r22
      111 - r07 / \ r07+r03 = r17 /   \ r17+r15 = r27 /         \ r27+r23
    
Producing these tables is actually what ultimately helped me finally realize
what was going on. So many articles explaining Cooley-Tukey made it sound like
the symmetry in the output because of the because of the Nyquist frequency was
what enables Tukey-Cooley to function. But that is in fact just a side effect
and isn't critical to the operations of Tukey-Cooley. It bothered me that I
couldn't figure out why \\(i\\) and \\(BinCount-i\\) being identical, which is
true, had to do with the fact that the code was using \\(n\\) and
\\(n+\\frac{RoundSize}{2}\\).

## Bit reversing

The other big part of FFT worth explaining is that it requires reordering the
inputs to make everything work correctly.  The number sequence to me seemed
odd. In an 8 element FFT, the inputs get reordered to 0, 4, 2, 6, 1, 5, 3 7.
While I could guess why 0 and 4 would need to be next to each other, why was 2
and 6 next?

If you recall from above, the property that enables Cooley-Tukey to work is the
relationship between \\(i\\) and \\(i + \\frac{BinCount}{2}\\). The point of
rearranging the inputs is to simplify access both initally and for each
successive round. If we don't reorder, we have to determine which two elements
are needed for each round during the round. That's not very optimized, but we
can calculate it all ahead of time.

In order to achieve this pre-calculation, let's think about the requirements for
each round. For the first round, the related pairs are always
\\(\\frac{BinCount}{2}\\) apart. So in an 16 element FFT, that's 8. So we have
to put 0 and 8, 1 and 9, 2 and 10, etc. together:

     0 (0000)   1 (0001)   2 (0010)   3 (0011)
     4 (0100)   5 (0101)   6 (0110)   7 (0111)
     
     8 (1000)   9 (1001)  10 (1010)  11 (1011)
    12 (1100)  13 (1101)  14 (1110)  15 (1111)

For the second round, we need to have groups of 4 where each source element is
\\(\\frac{BinCount}{4}\\) apart. In a 16 element FFT, this is 4, so we need 0
and 2, 1 and 3, 4 and 6, 5 and 7 together. But now we're at an interesting
intersection because in round 1 we want 0 and 4 together, but now we want 0 and
2 together. We can't put both 4 and 2 right next to 0 in the array.

     0 (0000)   1 (0001)   4 (0100)   5 (0101)
     2 (0010)   3 (0011)   6 (0110)   7 (0111)
     
     8 (1000)   9 (1001)  12 (1100)  13 (1101)
    10 (1010)  11 (1011)  14 (1110)  15 (1111)

What we can do however is separate them by some amount so each round needs to
add more to get the other element. So for round 1 we add 1 to index and get the
other (or odd in FFT terminology) element. For round 2, we place the next item 2
elements away. That means for the second round we could lay it out as so, which
would allow us to reach each pair by adding a single amount to each index each
round.

     0 (0000)
     8 (1000)
     4 (0100)
    12 (1100)
     1 (0001)
     9 (1001)
     5 (0101)
    13 (1101)
     2 (0010)
    10 (1010)
     6 (0110)
    14 (1110)
     3 (0011)
    11 (1011)
     7 (0111)
    15 (1111)

Now for the last round, we need to have 2 groups of elements. The result of that
is that each element needs to be paired with the element that is 1 apart. To
keep the system we've established going, we'll need to separate them by
\\(\\frac{BinCount}{2}\\) in the reordered array. In our 16 element FFT, that
would be 8. Our final order that enables us access the correctly paired elements
is:

     0 (0000)
     8 (1000)
     4 (0100)
    12 (1100)
     2 (0010)
    10 (1010)
     6 (0110)
    14 (1110)
     1 (0001)
     9 (1001)
     5 (0101)
    13 (1101)
     3 (0011)
    11 (1011)
     7 (0111)
    15 (1111)

What you may have noticed is that I've included the bit patterns, and for good
reason. If you look at the bits as least-significant first (left-to-right)
instead of most-significant (right-to-left), you'll see that it's counting in
binary.

Once I started to look at this, it started to make sense. The first thing that
needs to happen is that we need to alternate by 1 in the working array, then by
2, then by 4, all the way up to \\(\\frac{BinCount}{2}\\), or
\\(2^{BinCount-1}\\). All the while the input array needs to alternate first by
\\(\\frac{BinCount}{2}\\), then \\(\\frac{BinCount}{4}\\), all the way down to
1.

So what we're doing is reversing the bit pattern of the input elements to match
the pattern we need in order to minimal calculations in the FFT's inner loop.

## Conclusion

I hope that I've bridge the gap between what DFT is doing and how it's magic
works. The next post will be much more pragmatic, containing a lot more code.
I'd like to conclude by sharing with you how visualize and think about both DFT
and FFT. The analogy is not perfect, but it's how I visualized how it operated
while learning how it worked.

I like the illustration of the waveform being a bendable string that will neatly
wrap in a circle, carefully keeping each data point the correct distance from
the center as it is is turned around in a circle. How tight or loose around this
circle represents a frequency. As the waveform wraps around the circle, you can
sum every \\(x\\) and \\(y\\) position that each point lands at. I like to think
of this sum as the center of gravity. Using this center of gravity, we can
determine how strong the signal is at that frequency.

Doing that is rather slow however. The process can be sped up because of several
properties that hold when the output frequency count is a power of two.
Since all of the angles around the circle end up being repeated in very
predictable ways, we can find the center of gravity for all of these output
frequencies by building them up in pieces. Adding smaller sets together and
rotating related sets using Twiddle Factor allows the correct result to be
calculated quickly.

I like to think about it as a set of papers being copied to each other in a
systematic fashion. First the data points are put on each piece of paper, one
paper per input data point. Then for each round two pieces of paper are paired
together and the data points are copied, each paper rotated slightly
differently before being copied. Each paper represents the sum that's being
built up, and once the copying is done, you have the final value.
