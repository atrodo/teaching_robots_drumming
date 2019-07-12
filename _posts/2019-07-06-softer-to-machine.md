---
layout: default
title:  "Softer to Machine"
song: "Softer to Me by Relient K"
---

At the end of the last post I was just wrapping up getting the streaming
working. I did some cleanup of that then moved on to some house keeping I had
been meaning to do to try and improve the general health of the code base. None
of the issues were particularly important, but it provided a nice pause before
working on the next thing.

First I added create and modify time column to every table that is reasonable
for. I find these very handy long term, but forgot about them initially. Once I
did remember to add it, I tried to quickly add on. However, I discovered
something new about SQLite I did not know before; SQLite cannot add columns with
a dynamic default value. If you try to add a column whose default is
"CURRENT_TIME", you're presented with this error:

  Cannot add a column with non-constant default

A quick search finds the [Stack Overflow
answer](https://stackoverflow.com/q/11631390) that helped me understand that
SQLite doesn't actually alter the table until the row is either read or
modified; I'm unclear which. But that means that "CURRENT_TIME" can't be the
default value since it won't actually be the time of the alter.

At that point, I left the create and modify time columns off knowing I'd have to
invest a little bit of time into getting the columns added. Since I had the time
now, I decided that it was a good time to work on adding those columns.
Initially, I thought all I had to do was:

1. Add the columns as nullable (it is not-null)
2. Update every row to "CURRENT_TIME", the value
3. Alter the column to make it not-null again

I had it in my head that it couldn't use "CURRENT_TIME" on a not-null column.
That, however, is incorrect. It can't add a default "CURRENT_TIME" at all, which
makes perfect sense in retrospect. A nullable field doesn't mean that null is an
acceptable default after the alter table, which for some reason I thought it was
an that sequence would be able to create the column as null.

Instead what I had to do was the recommended sequence of create new table with
new columns, copy data, drop original table and rename new table. Since I was
doing this to most of my tables, I was able to enlist the help of [Deployment
Handler](https://metacpan.org/pod/DBIx::Class::DeploymentHandler) to generate
the bulk of the script. I then had to do some manual updating of scripts, but I
was able to get it working.

After that, I moved on to a convenience for my interactive command processor:
using [short codes]({{ site.baseurl }}{% post_url 2019-04-27-the-moshing-short
%}) in addition to UUIDs when doing lookups of rows. I accomplished that first
by automatically adding the short_code column to every table with exactly 1
primary key and populating it automatically when short_code is accessed. Then I
overrode
[DBIx::Class::ResultSet->find](https://metacpan.org/pod/DBIx::Class::ResultSet#find)
so that if it gets a single parameter, it checks to see if it can parse a UUID.
If it's not, it assumes it's a short_code and changes the query to `{ short_code
=> $id }`. That went pretty straight forward, except initially, because of
aliasing, I accidentally changed the single parameter to a hash. Oops! Luckily
that was an easy fix.

The last thing I worked on was the database size. After a casual `ls -l`, I
noticed that my SQLite database was 220M in size. That was quite a bit higher
than I was expecting. After some investigation, I discovered
[sqlite3_analyzer](https://www.sqlite.org/sqlanalyze.html) and used it to
pinpoint the table that was the largest. That table, SongVoice, held the basic
data that I use to generate the music for each song, stored as a JSON hunk that
gets inflated when constructing the song. Which works well when I need the data,
but I don't always need it and it's a perfect candidate for compression.

I was already using DBIx::Class's
[InflateColumn](https://metacpan.org/pod/DBIx::Class::InflateColumn) feature to
decode the JSON stored in the database to into a perl hash, and then encoded it
back to JSON when updating the column. I added additional code on the round trip
to compress and decompress the JSON string.

Since I will often run `select(*)` manually on an entire table, I'm not a fan of
binary data in columns. Because of that, I also base64 encoded the compressed
data as well. This part is less than ideal for two reasons. First, it does
inflate the data that I'm trying to minimize so I don't get all the gains I'd
like. Second, since the column is so large, my `select (*)` is still  very
chatty with data I don't want. At some point, I'm going to move this data to a
surrogate table that just contains this data, which will allow me to stop base64
encoding the compressed data.

When I started, there were 96 rows consuming 97% of the entire database's file.
That's an average of 2.1M per row. After applying the compression and base64,
the database file shrank down to 75M, or a 33% of the original size. If I just
gzip the SQLite database, it shrinks to 55M, or 25% of the original size, so it
was a step in the right direction of reducing the database size.
