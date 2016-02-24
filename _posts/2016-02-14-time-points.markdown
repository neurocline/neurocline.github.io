---
layout: post
title:  "Time points"
date:   2016-02-14 01:00:00 AM
categories: dev
tags: programming time
---

Part 1 in a series.

Measuring points in time is conceptually simple, and yet we've managed to make a big mess of it
over the past few thousand years. I think it can be fixed. I'm going to present a system that is
always correct but can also be used with any past, present or future method of labelling moments
in time. In the process, we're going to talk about many of the ways that we have been measuring
and labelling time, because no new system can be used in a vacuum, we have to work with existing
systems.

# The problem with time

The universe was born, we think, in a big explosion 13.82 billion years ago, and time has been
marching steadily on ever since, relativity notwithstanding.

At first, our issues with measuring time and labelling points in time were that we had very
poor instruments to do so. Initially, we looked in the sky and declared time based on the position
of the sun in the sky. Since we didn't move around much, we didn't realize that the sun is
in different positions in the sky based on where on Earth you are located; noon for the Egyptians
was early morning for Britons. Label this problem 1.

As we got more accurate at measuring time, we noticed that the point of noon shifts; since the
Earth's axis is not at right angles to the orbit around the sun, the length of the day grows and
shortens during the orbit (a solar day differs by about 20 seconds from aphelion to perihelion).
Eventually we stopped trying to measure the day by the sun and just declared "a second is this
arbitrary fixed amount", "a day is 86400 seconds", "a year is 365 days".

Of course, the orbit of the Earth around the sun isn't exactly 31,536,000 seconds. Instead, it's more
like 31,558,118 seconds. So first we introduced leap years to add a day every 4 years, then we removed
leap days every 100 years, then we added back a leap day every 400 years, and then we fiddle with
leap seconds to further fine-tune it, all to keep noon being noon.

And then, of course, the Earth is actually slowing down - the day is about 2 milliseconds longer compared
to when we first set up the value for a second as an absolute value instead of something relative
to the Earth's rotation around its axis and orbit around the sun.

And we haven't even left the Earth yet, to any appreciable degree. Imagine how much more complicated
it will be when some of us are living on Earth, others on Mars, and others in habitats in space either
orbiting planets or our sun or even traveling between stars or orbiting other stars.

# Absolute seconds

We have an absolute second - we haven't fiddled with its definition since it was first formally
created in 1967 as the number of cycles of radiation from a specific Cesium-133 transition. If you're
curious, the definition is this: "The second is the duration of 9,192,631,770 periods of the radiation
corresponding to the transition between the two hyperfine levels of the ground state of the caesium-133
atom". TAI, or International Atomic Time

Since we have absolute seconds, and we know the age of the Universe down to a pretty precise value,
in some respects we have an absolute time scale that we can use, assuming we can figure out how to
handle relativity. It might be something akin to what we did with UTC, we picked a mostly arbitrary
point and said "here is zero". So we might pick some patch of space and say "here is the standard
clock, all other times are relative to this point in space". We can ignore that for now, though,
and just fix our time issues sans worrying about relativity's effect on the "absolute" value of time.

If we had started with this, we would be done. Except, there are reasons for all our relative measures;
large absolute numbers are hard to use and would create new problems. More importantly, we can't erase
the past, and we don't have enough power to mandate a switchover.

Still, keep this in mind. There is some absolute scale of seconds; it is linear, and it goes back to
the origin of the universe. We really don't even care what that origin point is; we can pick some
arbitrary point in time in recent history, call it zero, and then times before that point are simply
represented by negative numbers. So let's pick the point at which TAI really came into existence in the
absolute form we have now, 1 January 1977 00:00:00.

Here is the important point to keep in mind for the next sections: there is an absolute and linear time
scale, even if we have a hard time measuring it. All of our measurements will be approximations to
the real time with some degree of error.

This is not a new concept in and of itself (see Terrestrial Time). But we need some layers so that we
can use it in our day-to-day life.

# Bibliography

[Is the International Atomic Time TAI a Coordinate Time or a Proper Time?](http://adsabs.harvard.edu/cgi-bin/nph-bib_query?bibcode=1986CeMec..38..155G)

[Terrestrial Time](https://en.wikipedia.org/wiki/Terrestrial_Time)

[Splitting the Second: The Story of Atomic Time](ftp://pvictor.homeftp.net/public/Sci_Library/Phys%20Library/PPop_Popular-level/Jones%20T.%20%20Splitting%20the%20second%20-%20the%20story%20of%20atomic%20time%20(IOP,%202000)(202s).pdf)

[CppCon 2015: Howard Hinnant “A C++14 approach to dates and times"](https://www.youtube.com/watch?v=tzyGjOm8AKo)

[chrono-Compatible Low-Level Date Algorithms](http://howardhinnant.github.io/date_algorithms.html)

[Time&mdash;From Earth Rotation to Atomic Physics](http://dl.yazdanpress.com/BOOKS/PHYSICS/Time_From_Earth_Rotation_to_Atomic_Physics(marked).pdf)
