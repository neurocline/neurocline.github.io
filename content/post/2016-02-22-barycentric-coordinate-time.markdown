---
categories: dev
date: 2016-02-22T17:32:00Z
tags: programming design
title: Barycentric Coordinate Time
url: /2016/02/22/barycentric-coordinate-time/
---

We have half of a better computer timestamp system. Barycentric Coordinate time, or TCB, is defined
as the SI second, duration of radiation periods of cesium centered at the spacetime coordinate of
the geocenter of the solar system at 1977 January 1, 0h 0m 32s.184 TAI; in other words, an atomic clock
centered here starting at the given time would count TCB time. This time was picked to provide a bridge
between current standards and TCB time.

[Time: from Earth Rotation to Atomic Physics](http://dl.yazdanpress.com/BOOKS/PHYSICS/Time_From_Earth_Rotation_to_Atomic_Physics(marked).pdf), page 118.

I further propose that for computer timestamps, we use integers of the duration of radiation periods
of cesium, which is precisely 9,192,631,770 ticks per second. There are transforms for general
relativity to treat this properly for any point in the solar system, and we should be able to
extend it beyond the solar system as we start travelling, without too much effort (relative to the
effort of actually travelling beyond the solar system, of course). A 96-bit integer can hold a comfortably
large range (273 billion years), and a 128-bit value would be able to date every event in the lifetime
of the universe, at least as we currently understand it,
A 64-bit value can only hold a timespan of 63 years at this tick rate, so that would be infeasible, although
maybe acceptable in specific use cases.

Note that TCG, or Geocentric Coordinate Time, was also defined at this point, and there is a (admittedly nonlinear)
transformation between TCG and TCB. But we don't want to be using TCG time, as this is Earth-centric, and
some software we are writing now will almost certainly be used other than on or around Earth.

Also note that other clocks than cesium are being considered; if someday we switch to a higher-resolution clock,
there would be a simple linear transform, since almost certainly that system will hold onto the SI second.

Finally, we will probably have Mars Time, Moon Time etc alongside Terrestrial Time, further complicating things
and thus further driving home the point that computer timestamps should switch to TCB time now.

So, to summarize, a modern computer timestamp is

- a 128 bit value (96-bit is optional but could cover most time ranges)
- zero at 1977 January 1, 0h 0m 32.184s
- clock located at the geocenter of the solar system
- tick count is 9,192,631,770 ticks/second

There is a bridge from UTC to TAI to TCB. I don't care at all about civil time, or rather I don't want to be
measuring time events in civil time (calendars and the like). TCB is irrelevant of geographical concerns.

# Reference

[International Atomic Time](https://en.wikipedia.org/wiki/International_Atomic_Time), TAI time

[Geocentric Coordinate Time](https://en.wikipedia.org/wiki/Geocentric_Coordinate_Time), TCG time

[Barycentric Coordinate Time](https://en.wikipedia.org/wiki/Barycentric_Coordinate_Time), TCB time
