---
layout: post
title:  "A history of the Internet"
date:   2015-11-02 08:36:00 AM
categories: dev
tags: internet network
---

Recently, an ill-worded phrase by a near-friend of mine ("TCP/IP, a poorly designed protocol"),
and his overlying thesis ("95% of the work in developing networking was done by commercial
companies, DARPA shouldn't get credit for the Internet"), caused me to do a lot of reading. I
didn't start hard-core software development myself until 1981, so while I knew a lot of this
anecdotally, I quickly realized that I didn't know as much as I thought.

I need to write all this up, but here's a few nuggets.

1. The French nearly invented the Internet.
1. Virtually all of the work on networking was funded by governments.
1. The sole exception was Xerox PARC, but Xerox shot themselves in the foot.
1. Davies, a Brit, came up with packet switching in 1959 (although perhaps it was simulataneously conceived of by Paul Baran).
1. Cerf et al worked on creating ARPANET, a packet-switched network, but with a host-to-host, or connection-oriented, system called NCP, first public demonstration in 1972 (showing email, among other things).
1. The University of Hawaii launched ALOHANet, a radio network using random packet transmission, in 1970.
1. Then Louis Pouzin, French, came up with datagrams and smart edge/dumb router, the key innovations that turned ARPANET from unworkable to unbeatable.
1. Meanwhile, Metcalfe, having done his Ph.D. thesis on what became Ethernet, was offered a job and major support by Xerox PARC; Ethernet was born in 1973.
1. The French government and European industry said "nope, we like circuit switching just fine", and even Pouzin himself became convinced, eventually abandoning his approach. But the ARPANET group saw it as a great advance.
1. Then ARPA funded the rest; TCP/IP was invented, coded up, and eventually ARPANET switched over to it.
1. BSD released a TCP/IP stack around 1983 that became the most influential networking stack in history.
1. The actual opening of ARPANET to become the Internet we know was directly due to a $600 million US funding effort willed into existence by Al Gore.

In particular, TCP/IP had about 5 years of careful thought and initial development put to it,
preceeded by 5 years of experimentation and trial, and then another 10 years to roll it out.
Like many innovations that sprang out of nowhere, it did nothing of the same. From original
idea to world-beating dominance was a 20-year timespan of thinking and implementing.

Note that TCP won because government funding shielded its development from the inter-company
and inter-country politics that caused OSI to eventually become completely irrelevant. And
the government subsidies meant that Interent protocols could be implemented for free, versus
having to buy very expensive ISO docs for OSI. I doubt many of you have purchased ISO docs.

I like many of the write-ups I've read, and the few books on this, but I think there's a good
business thesis here that goes against the modern interpretation of the startup or corporation
doing all of the work. Certainly, government funding is pitiful compared to what it was in
the 20th century, and definitely in the period from 1950-1990. But what I see in real companies
is that "works in practice" is not just important but "works in theory" is denigrated, and
companies quickly reach local maxima and get stuck in a web of their own making, and stop
advancing. As I re-read the history of efforts by IBM, DEC, ITTU and others, I see that
clearly had happened, and I see it happening today, just less clearly.

In the past, people published early results, freely talked to each other, and as a result
progress was rapid. Of course, people built on your results, and if you weren't fast, your
work benefitted others far more than yourself. In the modern era, there is relatively little
sharing, and while progress is still happening, it's starting to slow down. There are a host
of factors, but large among them is that companies like Apple are very secretive about what
they do, and allow no one to publish anything. Combine that with the relative lack of
government funding, and it's sad.

In particular, we should take the lessons of OSI versus TPC to heart. OSI was, in the end,
an attempt by dominant vendors to hold onto their profits. Since telecommunications companies
had circuits, naturally virtual circuits were the right answer, because they could still own
connections. If they had won, we would have a fraction of the world we have today. Vested
interests will rarely make good decisions. Just like the movie industry saw the VHS tape
as a disaster, failing to see that it would cause their revenues and profits to skyrocket, the
telecommunications vendors saw packet-switched networks as threatening their very existence.
This is true for all the vendor-developed network systems; all were invented to prop up
current modes of hardware production, or to allow them to muscle into the market as a new
competitor.

Things will change. Telecommunications went from fairly open to very regulated and closed off,
and it took a separate challenger (computing) to change that. Now computing is moving towards
being closed off, but eventually we'll see some other newcomer blossom and hijack attention
and create progress. Maybe the Maker movement, or home bioengineering, will be that vector.

## Reading list

[Donald Davies](https://en.wikipedia.org/wiki/Donald_Davies) developed the initial idea of packet-switched networks.

[CYCLADES](https://en.wikipedia.org/wiki/CYCLADES) project that created datagrams and moved thinking to the edges.

[A HISTORY OF COMPUTER COMMUNICATIONS: 1968 -1988](http://www.historyofcomputercommunications.info/index.html), book by Jim Pelkey, well worth reading.

[BBN Internet Engineering Timeline](http://xbbn.weebly.com/bbn-internet-engineering-timeline.html). BBN was one of the government-funded companies at the heart of early network development.

[RFC 801](https://tools.ietf.org/rfc/rfc801.txt). Transitioning ARPANET from NCP to TCP.

[Moving beyond TCP](http://rina.tssg.org/docs/PSOC-MovingBeyondTCP.pdf), 2010, with some details on Pouzin's contribution to networking, but largely as a modern "TCP/IP is not good enough, let's replace it" proposal. Since one of the authors of this paper is John Day, the second half of the paper is a summary of his 2007 book.

[Patterns in Network Architecture](http://www.amazon.com/Patterns-Network-Architecture-Fundamentals-paperback/dp/0137063385), 2007, John Day, is a book both on reimagining networking, but also a good history of the development of TCP/IP and the Internet.

[Does anyone still use TokenRing?](http://www.techrepublic.com/blog/classics-rock/does-anyone-actually-still-use-token-ring/). TokenRing was IBM's attempt to compete with Ethernet, developed in the early 1980s as a superior approach, but a combination of royalty pricing and Ethernet advancing far past TokenRing speeds killed it.

[OSI: The Internet that wasn't](http://spectrum.ieee.org/computing/networks/osi-the-internet-that-wasnt)

[Inventing the Internet](http://www.amazon.com/Inventing-Internet-Inside-Technology-Abbate/dp/0262511150), 1999, Jane Abbate.

[Open Standards and the Digital Age History, Ideology, and Networks](http://www.cambridge.org/us/academic/subjects/history/twentieth-century-american-history/open-standards-and-digital-age-history-ideology-and-networks), 2014, Andrew L. Russell.

[Microsoft's Contribution Was TCP/IP](http://www.circleid.com/posts/84259_microsofts_contribution_tcp_ip/), 2008, positing Microsoft helped put TCP/IP on the map.

[History of the Internet](http://www.newmedia.org/history-of-the-internet.html).
