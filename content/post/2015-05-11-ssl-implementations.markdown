---
categories: dev
date: 2015-05-11T15:49:00Z
tags: crypto, tls
title: SSL/TLS Implementations
url: /2015/05/11/ssl-implementations/
---

## Botan

[Botan home page](http://botan.randombit.net/)

[botan mirror on github](https://github.com/randombit/botan)

Strictly speaking, Botan is a general-purpose crypto library, but has a
TLS implementation. Source is kept in Monotone, but there is a Github
mirror. Botan has had continuous development since 2001. Is it safe to
use in a commercial product? Good question.

It builds on Unix/POSIX and Microsoft Windows. There are instructions for
both iOS and Android builds referenced in the documentation.

### Reference

* Wikipedia - [Botan](http://en.wikipedia.org/wiki/Botan_(programming_library))
* [SSL implementations compared](http://tstarling.com/blog/2014/04/ssl-implementations-compared/) - although this is really about coding style.
* Stack Overflow - [Looking for a High Level C++ SSL Library](http://stackoverflow.com/questions/4236526/looking-for-a-high-level-c-ssl-library)
* [Speedtest and Comparsion of Open-Source Cryptography Libraries and Compiler Flags](https://panthema.net/2008/0714-cryptography-speedtest-comparison/) - by the author of CryptoTE.
* [SSL and TLS](https://archive.is/mstSu) - sample from old Botan manual
* [Botan’s Implementation of the McEliece PKC](http://www.cryptosource.de/docs/mceliece_in_botan.pdf) - cryptography resilient against quantum computers?
* [Building the Botan library for ios7 arm64](http://www.codeitive.com/0miVjWPVVj/building-the-botan-library-for-ios7-arm64.html)

## OpenSSL

### Reference

* [OpenSSL As A Filter (Or Non-Blocking OpenSSL)](http://funcptr.net/2012/04/08/openssl-as-a-filter-(or-non-blocking-openssl)/)
* [OpenSSL Related Links](https://wiki.openssl.org/index.php/Related_Links)

## LibreSSL

LibreSSL is a fork of OpenSSL done by the OpenBSD folks as a security-focused rewrite.
There is a new library coming called ressl, for reimagined SSL.

[LibreSSL](http://www.libressl.org/)

[goreSSL](http://www.tedunangst.com/flak/post/goreSSL)

## Schannel

Secure Channel or Schannel is the name of Microsoft's SSL implementation,
implemented as an SSP in their Security Support Provider Interface.

### Reference

* [Schannel](https://msdn.microsoft.com/en-us/library/windows/desktop/ms678421(v=vs.85).aspx)
* [Secure Channel](https://msdn.microsoft.com/en-us/library/windows/desktop/aa380123(v=vs.85).aspx)
* [Certificate Stores](https://msdn.microsoft.com/en-us/library/windows/desktop/aa374752(v=vs.85).aspx)
* [SSL/TLS Alert Protocol & the Alert Codes](http://blogs.msdn.com/b/kaushal/archive/2012/10/06/ssl-tls-alert-protocol-amp-the-alert-codes.aspx)
* [Cryptography API: Next Generation](https://msdn.microsoft.com/en-us/library/windows/desktop/aa376210(v=vs.85).aspx)
* [CSslSocket - SSL/TLS enabled CSocket](http://www.codeproject.com/Articles/1600/CSslSocket-SSL-TLS-enabled-CSocket)
* [SSL in WinHTTP](https://msdn.microsoft.com/en-us/library/aa384076.aspx)
* [How to add SNI client hello extension using Schannel API?](https://social.msdn.microsoft.com/Forums/en-US/69060634-9209-47d2-a03c-05042906af73/how-to-add-sni-client-hello-extension-using-schannel-api?forum=windowssecurity)
* [SSL with WinHTTP](http://stackoverflow.com/questions/7464860/ssl-with-winhttp)
* [Manually Validating Schannel Credentials](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378740(v=vs.85).aspx)
* [CryptoAPI Cryptographic Service Providers](https://msdn.microsoft.com/en-us/library/windows/desktop/bb931357(v=vs.85).aspx)

## Secure Transport

Secure Transport is Darwin/Mac OS X's name for SSL. Modern features are only supported
in Mac OS X 10.8 or later. Also annoyingly, you must put certificates in the Keychain
instead of bundling them with your program. But it's open source under the APSL 2.0
license.

[Secure Transport Reference](https://developer.apple.com/library/mac/documentation/Security/Reference/secureTransportRef/)

## PolarSSL

### Reference

[PolarSSL is now a part of ARM](https://tls.mbed.org/tech-updates/blog/polarssl-part-of-arm) - and [Hacker News comments](https://news.ycombinator.com/item?id=8652019)

## General

Wikipedia - [Comparison of TLS implementations](http://en.wikipedia.org/wiki/Comparison_of_TLS_implementations)

Note to self - don't allow SSL renegotiation. [Re: SSL renegotiation](http://www.postgresql.org/message-id/51DE30DF.3080604@chittenden.org)

[OpenSSL Cookbook](https://www.feistyduck.com/books/openssl-cookbook/)

[Network Security with OpenSSL](http://shop.oreilly.com/product/9780596002701.do)

[Cryptographic Key Length Recommendation](http://www.keylength.com/)

[Compare SSL libraries](http://curl.haxx.se/docs/ssl-compared.html)

[On the Uses of Cryptography in Industry](http://hmurat.bilkent.edu.tr/27122014-cryptography-industry.pdf)

[Security Now podcast, episode 449](https://www.grc.com/sn/sn-449.htm)

### Stuff from Daniel Bernstein

[Things that use Curve25519](http://ianix.com/pub/curve25519-deployment.html)

[A state-of-the-art Diffie-Hellman function](http://cr.yp.to/ecdh.html)

[Ed25519: high-speed high-security signatures](http://ed25519.cr.yp.to/)

[Things that use Ed25519](http://ianix.com/pub/ed25519-deployment.html)
