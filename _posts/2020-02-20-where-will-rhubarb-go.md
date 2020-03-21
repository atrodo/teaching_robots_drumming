---
layout: default
title:  "Where Will Rhubarb Go"
song: "Where Will They Go by Sanctus Real"
---

After my last big project of writing a melody extraction routine, I took a bit
of a break for a couple weeks. Understanding the code and how it works, and
especially the blog post related to the work I did there, was a bit draining.
Now I'm back to making robots making music.

In recent weeks I took the time to restructure a couple parts of my database, as
it was in desperate need of some cleanup. I moved several large columns of JSON
data into their own table. As a part of that move, I did a lot of work to reduce
what data was in each of those JSON columns. I did things like reducing data to
known arrays and not including data that could be calculated with other data.
Doing that should save a lot of data in these columns, which will be good
because I'm likely going to need it. I plan on generating a lot of data to
generate test songs for evaluation.

After I worked on that restructure, I imported some songs through the melody
extraction method I had created. That's when I discovered that it was doing
maybe a 30% job of finding the melody. Most of the time during the song it was
not discovering the melody at all, instead just finding what sounded like random
notes. To it's credit, it appears to be finding the length of each note most of
the time, it was just rarely the right note.

This felt like a bit of a set back. I was just getting ready to start generating
some test songs, but I had to regroup and work on the melody extraction again.
But it had to be done, so I started examining what it was producing, why it was
producing it and why that was wrong.

First thing I noticed was that once I scaled up to an entire song, the memory
usage of it was well over what I had expected. I had expected a large amount of
memory since I keep a whole 3-5 minute song in memory during processing, but
this was much large, and slower, than expected. So I adjusted the code some; I
started passing around an array-ref instead of returning arrays; I calculated
each note from the FFT array immediately instead of after all of the FFT was
calculated so I would only have 128 elements in an array instead of 8,192.

After I had it running quicker and actually able to finish every time, I noticed
that the beat finder was identifying some sections as low as 30
beats-per-minute. As a bit of context, most songs are in the 90-140 BPM range.
This was happening because it would get confused during long sections that had
less music happening. This caused my beat extractor to see some of the beats as
louder than the others and pick just them. So I added a requirement that the
beats must be between 50 and 400 BPM. That helped quite a bit get things into
the right range.

After that I had added an idea of a loudness gate I had been kicking around. The
idea is that once you hear a melody line, you focus on the melody no matter how
quiet it gets until something else significantly louder comes on. Explained a
different way, imagine the chorus of of your favorite song. Think about the part
of it When you have that couple beats of rest before the melody continues.
There's still music playing in that time, but you ignore it because it's quieter
and not the melody. So now, I track the peaks of the melody in this gate and
every beat slowly reduce the gate. If a note wants to be registered, it has to
have a larger number than that gate. That helped some, but I think there's more
I can do with it at a later date.

At this point, I had noticed that the code sometimes either chose the wrong
octave of a note, generally an octave higher, or would oscillate between two
octaves. For that I did two things; first I remove the power of the lower note
from the higher note, and second I maintain an array of which octave was
strongest and chose the octave that was the most pronounced.

The reason I decided to remove the power of the lower octave from the higher
note is because of overtones. Overtones are when one note causes a second note
to resonate; if you strike middle-C on a piano with a good amount of pressure,
it will cause the C-above-middle-C to also vibrate and play. This change is
meant to reduce that effect by removing an approximate amount of power from the
higher note that is really a result of the lower note.

The other fix I did was allow any octave of a note to continue the note as I
extract over each sub-beat. An array is maintained of each note that contributes
to the final note, and when the code decides to end the note, it chooses the
note that was the best note the most times over that period. For example, this
means if a note starts at A6, bounces between A5 and A6 for the first couple
sub-beats but settles on A5 for the remainder of the period, the code will not
chose A5 instead of discarding the first part of the note as every note is
required to be at least 2 sub-beats long.

Once I had all that done, I felt like I was extracting a good 60% version of the
test songs I had. That's not to say I'm out of ideas on how to improve, but for
now the progress feels good enough that I'm comfortable moving on to making
music with robots again.
