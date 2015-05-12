---
layout: post
title:  "Stream ciphers"
date:   2015-05-12 11:34:00
categories: encryption
---
![My helpful screenshot](/assets/RC4.jpg)

Stream ciphers are an approximation of one-time pads. RC4 is the most well-known
stream cipher, but on the edge of being insecure now.

## General

Wikipedia - [Stream cipher](http://en.wikipedia.org/wiki/Stream_cipher)

[Block and Stream Ciphers](http://www.tech-faq.com/block-and-stream-ciphers.html)

[CRYPTANALYSIS AND DESIGN OF SYNCHRONOUS
STREAM CIPHERS](http://www.cosic.esat.kuleuven.be/publications/thesis-124.pdf)

[Hash Functions and Block Ciphers](http://www.burtleburtle.net/bob/hash/index.html)

## RC4

RC4 is the official algorithm, designed by Ron Rivest in 1987. It was initially a
trade secret, but an anonymous poster publishe a description of it to the Cipherpunks
mailing list. The algorithm derived from that description was known as ARC4, for 
"Alleged RC4" (because RC4 was registered as a trademark by RSA). RC4 was historically
widely used in TLS, although now prohibited as of RFC 7465.

Wikipedia - [RC4](https://en.wikipedia.org/wiki/RC4)

## Spritz

Spritz is a 2014 upgrade of RC4 by Rivest and Schuldt.

[Spritz: A New RC4-Like Stream Cipher](https://www.schneier.com/blog/archives/2014/10/spritz_a_new_rc.html)

[Spritz—a spongy RC4-like stream cipher and hash function](http://people.csail.mit.edu/rivest/pubs/RS14.pdf)

## Salsa20

Salsa20 was designed by Daniel J. Bernstein.

Wikipedia - [Salsa20](http://en.wikipedia.org/wiki/Salsa20)

## ChaCha20

ChaCha20, closely related to Salsa20, was also designed by Daniel J. Bernstein, and might be slightly more secure than Salsa20. Google is now using this for Android.

[Google Swaps Out Crypto Ciphers in OpenSSL](http://www.infosecurity-magazine.com/news/google-swaps-out-crypto-ciphers-in-openssl/)

[ChaCha20 and Poly1305 for IETF protocols](http://tools.ietf.org/html/draft-nir-cfrg-chacha20-poly1305-02)

## SOSEMANUK

Wikipedia - [SOSEMANUK](http://en.wikipedia.org/wiki/SOSEMANUK)

[Sosemanuk, a fast software-oriented stream cipher](http://www.ecrypt.eu.org/stream/p3ciphers/sosemanuk/sosemanuk_p3.pdf)

## ISAAC

Designed by Robert Jenkins ("burtlebob") in 1996, and used in a handful of places,
including inside GNU Coreutils.

Wikipedia - [ISAAC](http://en.wikipedia.org/wiki/ISAAC_(cipher))

[ISAAC: a fast cryptographic random number generator](http://www.burtleburtle.net/bob/rand/isaacafa.html)

## Phelix

Designed by Doug Whiting, Bruce Schneier, Stefan Lucks, and Frédéric Muller.

Wikipedia - [Phelix](http://en.wikipedia.org/wiki/Phelix)

[Helix: Fast Encryption and Authentication in a Single Cryptographic Primitive](https://www.schneier.com/paper-helix.pdf)

## Solitaire

Designed by Bruce Schneier for Neal Stephenson to use in the book Cryptonomicon.

Wikipedia - [Solitaire](http://en.wikipedia.org/wiki/Solitaire_(cipher))

## One-time pads

This is the gold standard, but hard to use in practice, except in specific use cases.

[One-time Pad](http://users.telenet.be/d.rijmenants/en/onetimepad.htm)

[Cell Phone Opsec](https://www.schneier.com/blog/archives/2015/04/cell_phone_opse.html)
