---
layout: post
title:  "Making colors out of strings"
alt_title: "Colored Robots"
song: "Colored People by dc Talk"
---

While I've been refining my process for generating new samples to train my music
making robots, I've had to look at a lot of the video I've been producing. Right
now, I'm making a less than abstract interpretation of the audio. Quite the
opposite actually, it's just a reflection of the music being played. I want to
replace it eventually, but that's a slightly larger than I want to tackle now.
While it might seem that coloring each square a pre-chosen color would be
simplest, that didn't seem like much fun. Plus, since not everything will always
be ordered in the same way, doing it that way means things are not always
colored in the same way. So instead, I've decided to color each instrument based
on their strings.

I had been doing a bunch of optimizations of the code that generates the audio
and video in order to speed up the whole process. It was a fun diversion, and I
really sped up the entire process which was really satisfying. But while doing
that, I watched a lot of video that it's producing, and it was a sea of blue
dots. That lead me here, looking for how to turn a string into a color.

I searched online for code that had done this before.  I found a couple
libraries and I got a good idea of what I should do. Some of them got really
fancy, even going as far as to look for color words and use them. I didn't need
that, even if it was an interesting idea. Since none of the code was in `perl` I
decided to write one myself.

I used what appeared to be a common theme: Multiply each ordinal character by a
large prime and at the end extract the triplets of colors from a 24-bit number.
The one thing I didn't see in other libraries that I knew I wanted to make sure
happened was that the output color was something that resembled visible. So I
also added some code make sure at least some of the values were over a minimum
threshold.

So below I have reproduced my `str_to_rgb` function, commented to explain what's
happening. I hope you enjoy.

```perl
use List::Util qw/max/;

sub str_to_rgb
{
  # Get the string, if there is no, use a random number as the input string
  my $str = shift // rand;

  # Get the minimum valid value, at least one output must be greater than this
  my $min = shift // 63;

  # Set up our constants, the 24-bit mask and our prime
  my $mod   = 0xffffff;
  my $prime = 0x21facf1;

  # Split on each character and get the numeric value of each
  my @chars = map {ord} split //, $str;

  my $result = 0;

  foreach my $c (@chars)
  {
    # In order to minimize overflow, I'm only using the bottom 24-bit of this
    # multiplication of the input character and our prime
    $result += ( $c * $prime ) % $mod;
  }

  # We do one final mod before extracting the 8-bit components
  $result %= $mod;

  # Do some hackery to get each 8-bit component
  # This is a more concise way to write this:
  # ( ($result & 0xff0000) >> 16, ($result & 0xff00) >> 8, $result & 0xff)
  my @result = map { ( $result & ( 0xff << $_ ) ) >> $_ } ( 16, 8, 0 );

  # Make sure that the color is something that resembles visible by making
  # sure the largest value is over our threshold
  my $max = max @result;
  if ( $max < $min )
  {
    map { $_ += $min - $max } @result;
  }

  # Finally return it as a color triplet for consumption
  return @result;
}
```
