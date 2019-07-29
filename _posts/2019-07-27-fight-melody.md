---
layout: default
title:  "The Fight Melody"
song: "The Fight Song by Sanctus Real"
---

The melody of a song is, to me, the core musical part that really identifies
something as a specific song. It's the line that you hum when its stuck in your
head, and it's the sequence that helps you identify it from a million other
songs.

Up until this point, I have been using my current set of tools to generate all
of the data required to generate a song. While I have some neat initial
experiments, I decided I needed a way to start reducing the number of gears in
motion at once so I can focus one part of the system at a time.

After some consideration, I decided that the melody generation part of the
system was the best candidate. That's for a couple reasons; primarily because
supplementing it with known content, that of existing songs, seemed more
straight forward. Also, I felt that the tools I have makes the melody generation
the trickiest to get working. So starting with that allows me time to get the
other bits improved and working. That should help me to have a better shot at
getting good melody generation working down the line.

After some complicated ideas that lead no where, I decided to start by searching
for a way to import existing melodies of songs. My initial thought was MIDI
renditions of songs, but I worried about both finding good quality version of
songs and being able to reduce it to just the melody without the accompanying
parts. But before I was able to do too much research on MIDI, I came across an
easy to import cover of Dr. Wiley's Castle theme from Mega Man 2 that met all
the short term requirements.

I whipped up a script that read the music in and generated all the various data
structures I was already using. Once I had those, I was able to put them through
the existing process and created an ogg file ready to listen. It took a couple
tries, but I ended up with a decent sounding output. Good enough to call the
experiment a success.

I set about to adjust how songs were structured, adding a new type, melody, that
contains all the musical data as well as the general structure of the song. This
will largely replace my existing type that I was calling master_beat, which sort
of represented a melody but was really designed to be fed into the neural
networks.

Generating a new song from the existing bands with the new melody didn't produce
anything that's exciting to listen to yet, however I think there's some
generation issues because all of them are silent after about 20 seconds. I'm
pleased with this because this is exactly what the goal is; to allow more
controlled debugging by reducing how many gears are in motion at once.

With the new melody type, I extracted the melody out of each of my existing
songs and have listened to them. I have some investigation to do, but the
melodies by themselves are somewhat underwhelming. This confirmed my suspicion
that melody generation is going to be hard. At this point, I think it is a
possibility that I'll be archiving and removing the existing songs.
