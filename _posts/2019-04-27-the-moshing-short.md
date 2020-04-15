---
layout: post
title:  "Making short codes out of UUIDs"
alt_title: "The Moshing Short"
song: "The Moshing Floor by Steve Taylor"
---

A few years ago, for a product that is no longer around, I helped a co-worker craft a fun little function I've decided to recreate. The purpose was to create more human friendly code that we could throw up into URLs instead of UUIDs. We use a lot of UUIDs, and I'm a big fan of them. But they are ugly when they are put in URLs. For a lot of our products, that wasn't a big deal. For this particular product, we wanted to be more consumer conscience and friendly. So we devised a simple idea: generate a short code from the UUID.

The great thing about UUIDs is that it's 128-bits of effective randomness. So the idea was to XOR each (I think) 32-bit chunk with each other, giving us a new 32-bit value. Using that final value, we use a human-friendly alphabet (missing easy-to-mistake letters and numbers like i, l, and 1, etc) and Base-N encoded it. We would then check it against the database and if there was any conflicts, alter it slightly so it wouldn't conflict. The end result was something like 'zK4en', which was a lot better to read off then '6d2920bf-992b-b164-a207-8188293a99e7'. (To be clear, that's not a real example.)

For my current purposes, I just want a shorter identifier than the UUID. I don't care about reversibility, nor if there is a conflict (at this point) and being slightly longer would be acceptable. So I rewrote it from memory filling those requirements. I've included it below with commentary.

```perl
my @uc = ( 'A' .. 'Z' );
my @lc = ( 'a' .. 'z' );

# Why did I mesh upper and lower case letters? Because I could, no other reason
my @char = ( 0 .. 9, mesh( @uc, @lc ) );
my $char_len = scalar @char;

sub short_code
{
  my $self = shift;

  my $result = '';
  my $uuid;

  # This uses UUID::parse, which parse $self->id into $uuid and returns errors.
  my $rc = parse( $self->id, $uuid );
  die "Invalid UUID: " . $self->id
      if $rc != 0;

  # The UUID is 128-bits, so this line will deconstruct it into an array with
  # four 32-bit integers. From there, I'll reduce it down to the two 32-bit
  # numbers I'm going to use to encode the final output. I use xor (^) because
  # using binary or (|) or binary and (&) would saturate the number and encode
  # very similarly every time. With xor however, those random bits work to my
  # advantage. The reason I'm not doing this with one 64-bit number is because
  # unpack doesn't always have Q, the 64-bit version, so this way is clearer.
  my @unpack = unpack( 'LLLL', $uuid );
  my @parts = ( $unpack[0] ^ $unpack[2], $unpack[1] ^ $unpack[3] );

  # Finally, we do a simple base-N encoding, in this case it turns out to be
  # base-62. I do the encoding by dividing the current number by 62 and using
  # the remainder as an index into the array of available characters.
  foreach my $part (@parts)
  {
    while ( $part > 0 )
    {
      $result .= $char[ $part % $char_len ];
      $part    = int( $part / $char_len );
    }
  }

  return $result;
}
```
