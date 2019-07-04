---
layout: default
title:  "Robot I Did It Again"
song: "Hoopes I Did It Again by Relient K"
---

The feature I've been tackling is that I want to add the ability to stream the
songs that are created so I can reduce the feedback loop between generating a
song and testing it. So with one command I can tell the process to generate it
and listen to it immediately without switching programs and reloading it, it'll
just start playing. So I've been working on getting more async processing
working, but there have been some bumps in the road and some complications,
which has been more annoying than I think it should have been. I've been using
[AnyEvent](https://metacpan.org/release/AnyEvent) and
[UV](https://metacpan.org/release/UV), creating an async model that takes
commands from stdin as well IRC and is able to stream all at the same time.

To take a step to the side, I need to explain about evenfd. On linux, there is a
feature called eventfd, which is a nice event loop mechanism that makes IO and
signal polling more efficient. It is used by libuv on linux, so under the hood
it is what ultimately is driving my event loop.

However, eventfd has some implementation details that have bitten me and has
made it difficult for me to get everything working with relative ease and
simplicity. First of all, when a process with eventfd forks, the eventfd is
cloned and all events are left with it. The implication of this is that any
change to the eventfd in the child process affects the parent process. So when
libuv stops all of the events associated in the eventfd, it also stops those
events in the parent as well. This makes it difficult to do a fork and play nice
with an existing libuv loop.

My first attempt was to fork and do an infinite loop pushing either the
currently playing song or silence to the stream. It worked well, and I probably
should have stopped with that. But I felt that I should do this correctly, and
that meant do it so the code pushing to the stream wasn't just in a busy loop.
So originally when I was first playing around with pushing data into ffmpeg to
generate the output, I discovered that the code would often block when writing
to a (unix) socket to ffmpeg. I didn't want to hold up the entire process
because of that block, since async and blocking don't mix well.

The second attempt looked much the same, except instead of a busy loop I used
AnyEvent to send the music to the stream. This didn't work very well, since all
I was doing was adding another event to listen for, and it wasn't always clear
if the parent or child would process the event and the events seemed to get
cross. I wasn't very aware of what was going on, and I knew that things weren't
working well with the event loop. But what made me move onto a new system was
that my sqlite transactions were causing major issues. I have the code setup
such that every command that's processed is done in a transaction. The fork with
sqlite was not likeing that, so I needed to move the fork out of the
transaction.

The third attempt was built using a system that would drop down to the parent
loop after adding an anonymous function to an array. The main loop would then
fork, attempt to clean up the parent event loop and call the new function which
would install new events in a new event loop. This appeared to work at first, I
could start the stream, and the stream would work once. However, after that one
time, the script would stop responding to inputs. After a lot of debugging and
troubleshooting, this is when I discovered this issue with eventfd.

I ended up trying a variation on this where I would start all of my processes
all at once and signal between them to start or stop the stream. At this point,
the complexity of these systems, as well as the mounting amount of code sitting
around in an unknown state, was starting to get out of control. I pushed through
until I realized I had major complexities looming. Coordinating data between the
loops was looking to be difficult, but eventually solvable as long as I opened
all the file handles before the forks. The problem that stopped this variation
was that since I had to prefork each of these loops, the interactive event loop
didn't know the pid of streaming event loop. Which seemed fine, I would just
have a function that each loop could use to find the pid of each other. But, I
wouldn't have all the pids until after they were all done forking, so the lookup
would generally be incomplete.

That was the point that I decided the complexity was too high. I gave up on all
of it and really moved back to a variation of my first attempt. I put everything
back into a single event loop so all the data is in one process space. If the
file I'm attempting to send to the stream is over a certain size (64k right
now), I fork a new process and send the data to the stream from that child
process. If it blocks, no harm to the rest of the process.

However, in the child process, once it was done, I had to stop the child
process. The obvious solution was:

```
exit 0;
```

Which immediately caused the symptoms to all return. Thankfully, I had a bunch
of debug code still hanging around watching the events being indirectly removed
by object destructors. I then realized that cleanly existing in perl causes the
destructors to fire. In that list of destructors was a close of the event loop
and I was back to my original problem of the child process closing eventfd
events in the parent. So I solved it by instead coding it as:

```
exec 'true';
```

Which will not run any destructors and has exactly the same end result, except
it doesn't destroy the eventfd events. That makes it a win to me.
