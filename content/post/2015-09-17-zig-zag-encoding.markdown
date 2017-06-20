---
categories: dev
date: 2015-09-17T17:12:00Z
tags: algorithms
title: Zig-zag encoding
url: /2015/09/17/zig-zag-encoding/
---

Encoding numbers, these abstract counting things, as physical entities requires choices. Ignoring the whole
religious war about big-endian versus little-endian, or the long-since won battles about two's complement
versus anything else, we are left with "how many bits/bytes do we use?". If you pick a fixed-length, then
you limit the range of the number you store, and you potentially waste a lot of space.

Variable length numbers are a compromise - you have either an explicit length prefix, or you have some
sentinel that indicates the end of the number. However, you're faced with a challenge when it comes to
negative numbers - two's complement is good for fixed-length numbers, but you can't tell the difference
between a large positive number and a small negative number, leaving all negative numbers to be encoded
as giant variable length strings.

Google Protocol Buffers, for positive numbers, uses what they call Varint encoding, where there is a
sentinel: numbers are stored in little-endian format, 7 bits per byte, and the most-significant bit
is 0 for the last byte and 1 for all other bytes.

Reading and writing Varint-encoded numbers looks like this (warning: no checking! not production code!): 

```c++
void to_varint(uint64 n, unsigned char* buf)
{
    while (n > 127)
    {
        *buf++ = (n & 0x7F) | 0x80;
        n >>= 7;
    }
    *buf = n;
}

uint64 from_varint(unsigned char* buf)
{
    uint64 n = 0;
    int shift = 0;
    while (*buf & 0x80)
    {
        n |= static_cast<uint64>(*buf++ & 0x7F) << shift;
        shift += 7;
    }
    n |= *buf << shift;
    return n;
}
```

For signed numbers, it does what they call zig-zag encoding to interleave positive and negative numbers
so that small negative numbers are still stored with a small number of bytes.

This actually stores an absolute value of the number with the sign bit stored in the least-significant bit
of the first byte. The math looks like this

	zigzag = (n << 1) ^ (n >> (BIT_WIDTH - 1)

where `BIT_WIDTH` is the number of bits in your fixed-width number (32-bit, 64-bit etc). Remember that
arithmetic shift replicates the sign bit, so shifting right like this will create a number that is `000...00`
for positive numbers and `111...11` for negative numbers.

Reading and writing zig-zag encoded numbers looks like this (warning: no checking! not production code!):

```c++
void to_zigzag(int64 n, unsigned char* buf)
{
    uint64 z = (n << 1) ^ (n >> (BIT_WIDTH - 1));
    while (z > 127)
    {
        *buf++ = (z & 0x7F) | 0x80;
        z >>= 7;
    }
    *buf = z;
}

int64 from_zigzag(unsigned char* buf)
{
    uint64 z = 0;
    int shift = 0;
    while (*buf & x80)
    {
        z |= static_cast<uint64>(*buf++ & 0x7F) << shift;
        shift += 7;
    }
    z |= *buf << shift;
    return (z & 1) ? (z >> 1) ^ -1 : (z >> 1);
}
```

A real codebase would combine both together, but it would also handle buffer over/underrun too.

Note that it would be possible to store negative numbers with a smaller number of bytes with a little more sophistication,
and not require zigzag encoding: you store the number of bytes required by the absolute magnitude of the number, and on
reading, you pick up the MSB of the sequence of stored bytes and recreate the number. I suspect that zig-zag encoding is
used because the amount of code for encoding and decoding is actually less when expressed in a high-level language, and
perhaps faster even in assembly.

The code listed above could be made faster by operating on the data in 32-bit or 64-bit chunks. It would be interesting
to time naive versus sophisticated code to see how much it's worth doing it in a more complex fashion.

As a gateway to more sophisticated methods, look at Apache Orc: [Run Length Encoding](https://orc.apache.org/docs/run-length.html).
