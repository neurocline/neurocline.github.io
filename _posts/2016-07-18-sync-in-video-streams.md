---
layout: post
title:  "Sync in video streams"
date:   2016-07-18 19:37:00 PM
categories: dev
tags: links
---

Some useful acronyms:

* PTS: Presentation time stamp; used to order frames for display. [Wikipedia](https://en.wikipedia.org/wiki/Presentation_timestamp)
* DTS: Decoding time stamp; used to order frames for decoding
* VBV: Video Buffering Verifier; used to prevent underflow/overflow of MPEG bit stream
* PCR: Program Clock reference, base timme for PTS values. [Wikipedia](https://en.wikipedia.org/wiki/Presentation_timestamp)

FLV files have a CompositionTime field in the VideoData segment; this is PTS - DTS.

PTS and DTS should be 90Khz values. CTS is in units of milliseconds, so compositionTime = (PTS - DTS) / 90.0.

# Reference

[Tutorial 05: Synching Video](http://dranger.com/ffmpeg/tutorial05.html)

[understanding DTS PTS](http://ffmpeg-users.933282.n4.nabble.com/understanding-DTS-PTS-td940270.html)

[Timing and Buffer Control](http://www.bretl.com/mpeghtml/timebuf.HTM)

[MPEG transport stream](https://en.wikipedia.org/wiki/MPEG_transport_stream)

[Video compression picture types](https://en.wikipedia.org/wiki/Video_compression_picture_types)

[ISO/IEC 14496-12 Part 12: ISO base media file format](http://xcshen.blog.51cto.com/attachment/201104/2835389_1302074539.pdf)

[Information technology — Coding of audio-visual objects — Part 15: Advanced Video Coding (AVC) file format](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=6&ved=0ahUKEwish5mEiP7NAhUTwWMKHfsTCooQFghGMAU&url=https%3A%2F%2Fwww.itscj.ipsj.or.jp%2Fsc29%2Fopen%2F29view%2F29n12786t.doc&usg=AFQjCNEDeyVkuC8i_g3OU27P5kOHSxefvQ&sig2=YWML8aQq7xg7DmTlTIB3vg&bvm=bv.127178174,d.cGc&cad=rja)

[MPEG-4](https://en.wikipedia.org/wiki/MPEG-4)

[MPEG-4 Advanced Video Coding](http://mpeg.chiariglione.org/standards/mpeg-4/advanced-video-coding)

[Flash Video](https://en.wikipedia.org/wiki/Flash_Video)

[Adobe Flash Video File Format Specification Version 10.1](http://download.macromedia.com/f4v/video_file_format_spec_v10_1.pdf)
