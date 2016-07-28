---
layout: post
title:  "Video and containers"
date:   2016-07-28 09:50:00 AM
categories: dev
tags: links
---

# NALU, Annex B, and Start Codes

A start code is 2-3 0x00 bytes followed by a 0x01 byte.

A NALU representation without emulation prevention bytes is called a Raw Byte Sequence Payload, or RBSP.

The four-byte sequences 0x00000000, 0x00000001, 0x00000002, and 0x00000003 are illegal within a non-RBSP NALU. In order to turn an RBSP into a NALU with start codes, byte stuffing is employed by inserting an Emulation Prevention byte 0x03 after two consecutive 0x00 bytes. To reverse this, when reading the stream, remove any 0x03 seen after two consecutive 0x00 bytes.

To determine the length of a NALU, start with a byte stream (which will have a start code), skip to the next start code, and count the bytes in between.

In order to convert between Annex B and AVCC, convert between start codes and lengths, and remove/add emulation bytes.

An AVCC stream always starts with a specific header

- 8 bits = 0x01 (this is illegal in Annex B)
- 8 bits of avc profile (from sps[0][1])
- 8 bits of avc compatibility (from sps[0][2])
- 8 bits of avc level (from sps[0][3])
- 6 bits = 0b111111
- 2 bits of NALULengthSizeMinusOne (typically 3)
- 3 bits of 0b111
- 5 bits of number of SPS NALU (usually 1)
- Repeated per SPS
  - 16 bits of SPS size
  -Variable SPS NALU data
- 8 bits of number of PPS NALU (usually 1)
- Repeated once per PPS
  - 16 bits of PPS size
  - Variable PPS NALU data

FLV streams must be written as AVCC.

# tbr, tbc, tbn

tbn is the time base from the container.

tbc is the time base from the codec.

tbr is video frame rate guessed from the video stream. A note from the FFMpeg source:

- tbr is NOT the average frame rate, it is the smallest frame rate that can accurately represent all timestamps.

# Reference

[Video File Format Specification, Version 10, 2008](https://www.adobe.com/content/dam/Adobe/en/devnet/flv/pdfs/video_file_format_spec_v10.pdf). FLV and F4V file format spec.

[Version 10.1](https://github.com/smartdu/srs/blob/master/trunk/doc/video_file_format_spec_v10_1.pdf).

[OIPF, Release 2 Specification, Volume 2 – Media Formats](http://www.oipf.tv/docs/oipf-archive/OIPF-T1-R2-Specification-Volume-2-Media_Formats-v2_2-2013-05-15.pdf)

[Overview of the H.264/AVC Video Coding Standard](http://ip.hhi.de/imagecom_G1/assets/pdfs/csvt_overview_0305.pdf)

## ISO/IEC 14496, "Coding of audio-visual objects"

## Sound

[ISO/IEC JTC 1/SC 29/WG 11N2503-sec5](http://sound.media.mit.edu/resources/mpeg4/SA-FDIS.pdf). Information technology - Coding of audio-visual objects. Part 3: Audio. Section 5: Structured audio.

## Base ISO format

[ISO/IEC 14496-12](http://l.web.umkc.edu/lizhu/teaching/2016sp.video-communication/ref/mp4.pdf). Information technology — Coding of audio-visual objects — Part 12: ISO base media file format

- [ISO/IEC 14496-12:2012](http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip)
- [ISO/IEC 14496-12:2012/Cor 1:2013](http://standards.iso.org/ittf/PubliclyAvailableStandards/c063525_ISO_IEC_14496-12_2012_Cor_1_2013.zip)
- [ISO/IEC 14496-12:2012/Amd 1:2013](http://standards.iso.org/ittf/PubliclyAvailableStandards/c063187_ISO_IEC_14496-12_2012_Amd_1_2013.zip)
- [ISO/IEC 14496-12:2012/Amd 2:2014](http://standards.iso.org/ittf/PubliclyAvailableStandards/c063188_ISO_IEC_14496-12_2012_Amd_2_2014.zip)

### MP4

[ISO/IEC 14496-14](https://www.cmlab.csie.ntu.edu.tw/~cathyp/eBooks/14496_MPEG4/ISO_IEC_14496-14_2003-11-15.pdf). Information technology — Coding of
audio-visual objects — Part 14: MP4 file format.

### AVC file format

[ISO/IEC JTC 1/SC 29 N, ISO/IEC 14496-15:2014/PDAM 2](https://www.itscj.ipsj.or.jp/sc29/open/29view/29n14632t.doc). Information technology — Coding of audio-visual objects — Part 15: Carriage of NAL unit structured video in the ISO Base Media File Format, AMENDMENT 2: Carriage of AVC based 3D video excluding MVC.

[ISO/IEC JTC 1/SC 29 N, ISO/IEC 14496-15:2010/PDAM 2](https://www.itscj.ipsj.or.jp/sc29/open/29view/29n12786t.doc). Information technology — Coding of audio-visual objects — Part 15: Advanced Video Coding (AVC) file format, AMENDMENT 2: Carriage of high efficiency video coding (HEVC).

# Unsorted

[Possible Locations for Sequence/Picture Parameter Set(s) for H.264 Stream](http://stackoverflow.com/questions/24884827/possible-locations-for-sequence-picture-parameter-sets-for-h-264-stream)

[Network Abstraction Layer](https://en.wikipedia.org/wiki/Network_Abstraction_Layer)

[Emulation Prevention Question](http://forum.doom9.org/archive/index.php/t-165995.html)

[Intel Media SDK 2012 Developer’s Guide](https://software.intel.com/sites/default/files/m/b/a/5/b/5/43685-Intel_Media_Developers_Guide.pdf)

[MP4 / ISO 14496-12 : How do you find the video and audio access units?](http://stackoverflow.com/questions/8525824/mp4-iso-14496-12-how-do-you-find-the-video-and-audio-access-units)

[Parsing H264 in mdat MP4](http://stackoverflow.com/questions/5544696/parsing-h264-in-mdat-mp4/8527819)

[H.264 extradata (partially) explained - for dummies](http://aviadr1.blogspot.com/2010/05/h264-extradata-partially-explained-for.html)

[Chromium h264_to_annex_b_bitstream_converter.cc](https://src.chromium.org/svn/trunk/src/media/filters/h264_to_annex_b_bitstream_converter.cc)

[MythTV H264Parser.cpp](https://code.mythtv.org/doxygen/H264Parser_8cpp_source.html)

[Introduction to H.264: (1) NAL Unit](http://yumichan.net/video-processing/video-compression/introduction-to-h264-nal-unit/)

[FLV muxer libavformat/flvenc.c](https://nsl.cs.usc.edu/enl/trunk/aqua/loader/jni/src/libavformat/flvenc.c)

[H264+flv bad video stream](https://ffmpeg.org/pipermail/libav-user/2013-June/004980.html)

[Red5 Multi-User Application Framework](https://code.google.com/archive/p/comserver/source/default/source)

[Need to convert h264 stream from annex-b format to AVCC format](http://stackoverflow.com/questions/23404403/need-to-convert-h264-stream-from-annex-b-format-to-avcc-format)

[How to generate FLV stream from raw h264 which can be played by Actionscript NetStream?](http://stackoverflow.com/questions/11862666/how-to-generate-flv-stream-from-raw-h264-which-can-be-played-by-actionscript-net)

[h264 annexb bitstream to flv mux ffmpeg library](http://stackoverflow.com/questions/29751805/h264-annexb-bitstream-to-flv-mux-ffmpeg-library)

[How to mux live h264 stream in AnnexB nal unit format to flv container](http://stackoverflow.com/questions/37300963/how-to-mux-live-h264-stream-in-annexb-nal-unit-format-to-flv-container)

[H264 AVCVIDEOPACKET](http://livertmpjavapublisher.blogspot.com/2011/06/h264-avcvideopacket.html)

[Simple-RTMP-Server](https://github.com/ossrs/srs/tree/2.0release). This is the original, now defuct, but there are [forks](https://github.com/ossrs/srs).

[Assorted spec docs](https://github.com/smartdu/srs/tree/master/trunk/doc)

[filippobrizzi/raw_rgb_straming](https://github.com/filippobrizzi/raw_rgb_straming)

[ffprobe Documentation](https://www.ffmpeg.org/ffprobe.html)

[Building x264 on Windows with Visual Studio](http://siliconandlithium.blogspot.no/2014/03/building-x264-on-windows-with-visual.html)

[x264 Settings](http://birds-are-nice.me/publications/extremex264_1.shtml)

[Video Encoding Settings for H.264 Excellence](http://www.lighterra.com/papers/videoencodingh264/)

[Eliminating H.264 bitrate peaks with Handbrake](http://eldar.cz/kangaroo/binarni-sxizofrenie/howto-handbrake-limit-peak-bitrate.html)

[x264 rate control modes](http://stackoverflow.com/questions/18586700/x264-rate-control-modes)

[How to reduce latency when streaming x264](http://stackoverflow.com/questions/20051784/how-to-reduce-latency-when-streaming-x264)

[Matroska codec specs](https://matroska.org/technical/specs/codecid/index.html)

[FFmpeg Formats Documentation](https://www.ffmpeg.org/ffmpeg-formats.html)

[Basics of streaming protocols](http://www.garymcgath.com/streamingprotocols.html)

[h264 ffmpeg: How to initialize ffmpeg to decode NALs created with x264](http://stackoverflow.com/questions/14322670/h264-ffmpeg-how-to-initialize-ffmpeg-to-decode-nals-created-with-x264)

[Optimizing an H.264 video encoder for real-time HD-video encoding](http://www.diva-portal.org/smash/get/diva2:432684/FULLTEXT01.pdf)

[ffmpeg::avcodec_encode_video setting PTS h264](http://stackoverflow.com/questions/6603979/ffmpegavcodec-encode-video-setting-pts-h264)

[Using ffmpeg to encode a raw video to H.264 format](http://superuser.com/questions/322354/using-ffmpeg-to-encode-a-raw-video-to-h-264-format)

[transcode with ffmpeg](http://thompsonng.blogspot.com.au/search/label/FFMPEG)

[Processing EBU's YUV files with FFmpeg](https://www.tribler.org/FfmpegYuv/)

[19 FFMPEG COMMANDS FOR ALL NEEDS](http://www.catswhocode.com/blog/19-ffmpeg-commands-for-all-needs)
