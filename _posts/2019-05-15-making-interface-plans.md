---
layout: post
title: "Making Interface Plans"
alt_title:  "The Sympathy Robot"
song: "The Sympathy Vote by Steve Taylor & the Perfect Foil"
---

I am  continuing the march on reducing moving parts and migrating code from the
monster script to the database schema. I've now reduced much of the code
required to produce a song down to a few lines of code:

```perl
my $song        = $band->create_related('songs');
my $scode       = $song->short_code;
my $output_file = "output/test_$scode.ogg";
$song->create_file( $output_file );
```

And that will produce a nice little ogg file ready for listening. So far the
couple songs I've coaxed it to produce were not exactly catchy or even good,
but there are musical qualities to it.

As I start wrapping up the cleanup of the monster script into something smaller,
I'm starting to look at how to start managing the data in a meaningful way. The
old standby of course would be a web page, but that seems like a lot of
infrastructure to show this data, even if I'm very familiar with that
infrastructure. The other options that occurred to me were just doing command
line arguments to coax data out, which would be pretty simple for me at this
point. I could also produce an ncurses interface, but that would require
learning ncurses which might be fun. The craziest idea I've had was to create a
quick integration with slack to allow me to manage the data from within slack.
However, that'd require a webserver and the only thing it would save me over
generating a web page would be the html. I've had a lot of other, somewhat
desperate ideas like using a private irc server or integrating with an existing
[znc](znc.in) instance that I have. While these are technically feasible, and on
the surface look easier, sure look like just as much work when I think about the
details. That being said, I am a little excited about the challenge of it, but
it does feel like it will end up getting in the way of getting music generated.
