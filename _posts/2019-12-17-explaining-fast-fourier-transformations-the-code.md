---
layout: post
title: "Explaining Fast Fourier Transformations, Part 2; The Code"
alt_title:  "Botting Dancing Unicorns"
song: "Battle Dancing Unicorns by Five Iron Frenzy"
---

This is part 2 of a 2 part series about Discrete and Fast Fourier
Transformations. The first part is about the theory and how DFTs work while the
second part is about the practical application and code execution to get the
frequency data from a sound sample.

While in the last post I went over some very theoretical things, this post will
be much more pragmatic and have a lot of code in it.

## Common

The code below has been lightly modified and heavily commented compared to what
I have running now. For the core of the FFT algorithm I have two versions to
compare; the `perl` version is designed to be easier to read while combining
some very high level concepts into a small amount of space. The `C` version will
more optimized for speed and will map the high level concepts in `perl` down to
discrete steps. I've used the same variable names between versions so it will
be easier to reference what's happening between the two.

Since there is a lot of lookups used with Cooley-Tukey, I start by defining some
constants and shared lookup tables. I'll calculate these once and use them for
both the `perl` and `C` versions.

```perl
my $blk_bits = 12;
my $blk_size = 1 << $blk_bits; # 4096

my @cos  = map { cos( 2 * pi * $_ / $blk_size ) } 0 .. $blk_size;
my @sin  = map { sin( 2 * pi * $_ / $blk_size ) } 0 .. $blk_size;
my @rbit = map { oct( "0b" . unpack( "b[$blk_bits]", pack "S", $_ ) ) } 0 .. $blk_size;
```

Since my code really only concerns itself with one block size of FFT, we can
statically define its size. Since we are using a traditional Cooley-Tukey, it
must be a power of 2. That makes the first two statements simple.

The `@cos` and `@sin` arrays are generated for each element in a `$blk_size`
sized array. These will be used as the angle step amounts to progress around the
circle during each round.

Finally, the `@rbit` array is a lookup table so that I can easily translate
from the original array item to the reversed bit index. This is important
with Cooley-Tukey because we must rearrange the data before we do the FFT
calculation.

The code is very dense, so I think it'd be useful to break it down and heavily
comment it. The sequence is a map and series of function calls. In order to
facilitate the explanation, I'm going to work from the inside, out.  Each `...`
will replace the previous explanation's sequence. This will help demonstrate the
discrete steps that `perl` takes to generate the final array.

```perl
# Take the integer in $_ and translate it into an "unsigned short (16-bit) value"
pack( "S", $_ )
# We then unpack that 16-bit value into a bit string in ascending bit order
# This is actually where the reversing of the bits takes place, because
# lower-case b in unpack starts with the least significant bit when outputting
# them left-to-right
unpack( "b[$blk_bits]", ... )
# This will construct a bit string for conversion, '0b' starts a binary sequence
"0b" . ...
# oct translates from octal to a number, but can also translate binary or hex
oct(...)
# map each value from 0 to $blk_size (will contain 1 extra index)
map {...} 0 .. $blk_size
```

This is all thanks to just a few built in `perl` functions: [pack, unpack](https://perldoc.pl/functions/pack), [oct](https://perldoc.pl/functions/oct), and [map](https://perldoc.pl/functions/map).

## `perl`

Since `perl` is going to be more concise and easier to see from a high level, it
seems natural to start with there.

I have avoided using the terms real and imaginary in this code in order to try
and come up with something more conceptually descriptive: `xsum` and `ysum`.
Because that is what both arrays represent: the sum of the waveform across each
directions (`x` and `y`).  If, however, you're trying to connect this code with
another FFT tutorial, then `xsum` is equivalent to the real part and `ysum` is
equivalent to the imaginary part.

The `$stride` and `$half` variables needs some special attention. Each group is
divided into an even half (the first half) and an odd half (the last half). Both
halves play an important role in the calculation, and we only visit each pair
once per iteration. `$stride` defines the size of each group for each round, so
the first round has a group size of 2, so `$stride = 2`. Likewise, `$half`
represents the number of elements between the even, lower half and the odd,
upper half; so when `$stride = 2`, then `$half = 1`.

The last item to pay special attention to is `$lu_step`, the look-up step. We
only generated 1 set of sine and cosine tables, but depending on the size of
each group, we can still look up the relevant values by stepping through it so
that it starts at 0 and end at `$blk_size`. That will provide us with the
correct value for sine and cosine for that group.

For instance, when `$stride = 2`, `$lu_step` will be `blk_size / 2` or 2048.
This will give us the sine and cosine for 180°. On round 3, or when `$stride =
8`, `$lu_step` will be `blk_size / 8`. This will give us the sine and cosine for
the multiples of 45°.

```perl
sub ex_pp
{
  my @input = @_;

  # Initalize ysum to 0 and xsum to the input values in bit-reversed order
  my @xsum = map { $input[ $rbit[$_] ] } 0 .. $blk_size - 1;
  my @ysum = (0) x $blk_size;

  # We have as many rounds as we have bits in the calculation size
  # Start at 1, not 0, because we start with 2 elements in each group and the
  # group size must be an even number
  foreach my $bits ( 1 .. $blk_bits )
  {
    my $stride  = 1 << $bits;          # Size of each group
    my $half    = $stride >> 1;        # Distance in the array between each half
    my $lu_step = $blk_size / $stride; # Step between each @sin and @cos lookup

    foreach my $idx ( 0 .. $blk_size )
    {
      # We need to know which element inside the group we are at
      my $i = $idx % $stride;

      # Since we're just iterating over each item in the input array, we
      # skip half of the items with this conditional
      next
          if $i >= $half;

      my $even = $idx;             # The even element index we are working with
      my $odd  = $even + $half;    # The odd element index we are working with
      my $lu   = $i * $lu_step;    # The lookup index

      # This is the actual calculation meat. This is where we calculate the
      # "Twiddle Factor" or Root-of-Unity to rotate the existing sums by the
      # requisite degrees.
      my $x =  $xsum[$odd] * $cos[$lu] + $ysum[$odd] * $sin[$lu];
      my $y = -$xsum[$odd] * $sin[$lu] + $ysum[$odd] * $cos[$lu];
      $xsum[$odd] = $xsum[$even] - $x;
      $ysum[$odd] = $ysum[$even] - $y;
      $xsum[$even] += $x;
      $ysum[$even] += $y;
    }
  }

  # Finally, we calculate the magnitude of each FFT item using Pythagorean
  # theorem and only return the first half of the array.
  return [ map { sqrt( $xsum[$_]**2 + $ysum[$_]**2 ) } 0 .. $blk_size >> 1 ];
}
```

## `C`

On the opposite side, the `C` version of the exact same transformation was done
with much more optimization. I say that, however there is not much difference
between the `C` and `perl` version, primarily in the looping construct.

The big thing you'll notice right away is that I start with a bit of `perl`
code. I'm using
[Inline::C](https://metacpan.org/pod/distribution/Inline-C/lib/Inline/C.pod)
to load this code into one source file. This has the advantage of being able to
calculate all the tables concisely in `perl` once, and reuse them in `C` by
packing them into a format `C` has an easy time understanding. The overhead for
this is negligible since it's only done once and stored with a `state`.

```perl
sub ex_cc
{
  my $input = \@_;

  state $rbit = pack 's*', @rbit;
  state $cos  = pack 'd*', @cos;
  state $sin  = pack 'd*', @sin;

  my $sub = \&_ex_cc;

  my $result = $sub->(
    pack( 'd*', @$input ),
    $blk_size,
    $blk_bits,
    $rbit,
    $cos,
    $sin
  );

  return [ unpack 'd*', $result ];
}
use Config;
use Inline C => Config => ccflags => $Config{ccflags} . ' -std=c99';
use Inline C => <<'EOC';
   // C code below goes here
EOC
```

The main difference to notice in the `C` version is the looping construct I
used. In `perl`, I used a single `foreach` loop and used `next` to skip the
"odd" elements. In the `C` version, I used an inner and outer loop to do the
skipping. The speedup I received from doing it that way was significant enough
that I decided that the slightly more complex code was worth the speed increase.

```C
#include <math.h>
#include <stdint.h>

SV* _ex_cc(char *cinput, int blk_size, int blk_bits, char *crbit, char *ccos, char *csin)
{

  // Cast all of our inputs into proper types. Inline::C only handles a couple
  // of types, so I just use char* to pass it in and cast to the correct type
  int16_t *rbit = (int16_t *) crbit;
  double *input = (double *) cinput;
  double *cos   = (double *) ccos;
  double *sin   = (double *) csin;

  double xsum[blk_size];
  double ysum[blk_size];

  // Initialize our arrays
  for ( int i = 0; i < blk_size; i++ )
  {
    xsum[i] = (double) input[ rbit[i] ];
  }
  memset(ysum, 0, sizeof ysum);

  for ( int bits = 1; bits <= blk_bits; bits++ )
  {
    int size    = 1 << bits;
    int stride  = size >> 1;
    int lu_step = blk_size / size;

    // Here is the major difference in C, we do two loops instead of just
    // skipping the indexes that don't belong. It's a small optimization, but
    // significant enough that I did it instead of the perl version's more
    // clear version

    // base is for the base index, which is the index of the first element of
    // each group. The first round, base will be every even index. In the
    // second round, base will be every fourth index, etc.
    for ( int base = 0; base < blk_size ; base += size )
    {

      // idx is the index inside each group, only covering half of the group
      // since it will process two items, idx and idx+stride, at the same time
      for ( int idx = 0; idx < stride; idx++ )
      {
        int even = idx + base;
        int odd  = even + stride;
        int lu   = idx * lu_step;

        double x    =  xsum[odd] * cos[lu] + ysum[odd] * sin[lu];
        double y    = -xsum[odd] * sin[lu] + ysum[odd] * cos[lu];
        xsum[odd]   =  xsum[even] - x;
        ysum[odd]   =  ysum[even] - y;
        xsum[even] +=  x;
        ysum[even] +=  y;
      }
    }
  }

  // Prepare the output by producing the mangitude of each element
  double output[blk_size];
  for ( int i = 0; i < blk_size >> 1; i++ )
  {
    output[i] = sqrt(xsum[i]*xsum[i] + ysum[i]*ysum[i]);
  }

  // Finally return it all in a new SV (perl's scalar type)
  return ( newSVpv((double *)output, sizeof output) );
}
```

And that's it, that's how to implement Cooley-Tukey in both `perl` and `C`. I
hope that this has been informative and that the lessons I've learned can be of
use for you. One last piece of advice, if you're not interested in how FFT works
or want your own to use or learn from, I'd highly recommend just using
[FFTW](http://www.fftw.org/) instead. While this has been educational and
rewarding to do myself, my goals in doing this were mroe than just having a
working FFT . If your only goal is just to use a FFT, I don't recommend you
write and maintain one yourself.

If you do decide to use [FFTW](http://www.fftw.org/), please remember that it
doesn't give you the magnitude of each output frequency, you have to calculate
that yourself. That is a lesson that took me entirely too long to learn.
