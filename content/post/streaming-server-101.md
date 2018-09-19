+++
title = "流媒体服务器 101"
author = "Junahan"
date = 2018-09-19
keywords = ["RTP", "RTCP", "RTSP", "RTMP", "HLS"]
lastmod = 2018-09-19T11:05:24+08:00
tags = ["Streaming"]
categories = ["Streaming"]
draft = false
+++

## <span class="section-num">1</span> 摘要 {#摘要}

针对流媒体播放服务器和客户端做的一个调查汇总。


## <span class="section-num">2</span> H5 player {#h5-player}

-   [vedio.js](http://videojs.com) - Video.js is a web video player built from the ground up for an HTML5 world. It supports HTML5 and Flash video, as well as YouTube and Vimeo (through plugins). It supports video playback on desktops and mobile devices. This project was started mid 2010, and the player is now used on over 400,000 websites.
-   [hls.js](https://github.com/video-dev/hls.js/tree/master) - hls.js is a JavaScript library which implements an HTTP Live Streaming client. It relies on HTML5 video and MediaSource Extensions for playback.
-   [Shaka-player](https://github.com/google/shaka-player) - Google 开源的 H5 流媒体播放器， 支持 [Dash](https://dashif.org) 或者 [HLS](https://developer.apple.com/streaming/) 协议的媒体服务。
-   [Streamedian](https://streamedian.com) - Multifunctional HTML5 RTSP streaming video player over WebSocket.
-   [jPlayer](http://www.jplayer.org) - 一款 H5 媒体播放器，支持 mp3, mp4 (AAC/H.264), ogg (Vorbis/Theora), webm (Vorbis/VP8), wav
-   [pearPlayer.js](https://github.com/PearInc/PearPlayer.js) - 一个 p2p 多媒体播放器
-   AudioJs - H5 audio player


## <span class="section-num">3</span> Media Server {#media-server}


### <span class="section-num">3.1</span> FFmpeg Server {#ffmpeg-server}

[ffserver](http://ffmpeg.org/ffserver-all.html) is a streaming server for both audio and video. It supports several live feeds, streaming from files and time shifting on live feeds. You can seek to positions in the past on each live feed, provided you specify a big enough feed storage.


### <span class="section-num">3.2</span> Live555 {#live555}

The "[LIVE555 Media Server](http://live555.com/mediaServer/)" is a complete RTSP server application. It can stream several kinds of media file (which must be stored in the current working directory - i.e., the directory from which you launch the application - or a subdirectory.):

-   A MPEG Transport Stream file (with file name suffix ".ts")
-   A Matroska or WebM file (with filename suffix ".mkv" or ".webm")
-   An Ogg file (with filename suffix ".ogg", "ogv", or ".opus")
-   A MPEG-1 or 2 Program Stream file (with file name suffix ".mpg")
-   A MPEG-4 Video Elementary Stream file (with file name suffix ".m4e")
-   A H.264 Video Elementary Stream file (with file name suffix ".264")
-   A H.265 Video Elementary Stream file (with file name suffix ".265")
-   A VOB video+audio file (with file name suffix ".vob")
-   A DV video file (with file name suffix ".dv")
-   A MPEG-1 or 2 (including layer III - i.e., 'MP3') audio file (with file name suffix ".mp3")
-   A WAV (PCM) audio file (with file name suffix ".wav")
-   An AMR audio file (with file name suffix ".amr")
-   An AC-3 audio file (with file name suffix ".ac3")
-   An AAC (ADTS format) audio file (with file name suffix ".aac")


### <span class="section-num">3.3</span> Darwin Media Server {#darwin-media-server}

[Darwin Media Server](https://macosforge.github.io/dss/) is an open source server technology that allows you to send streaming media to clients across the Internet using the industry standard RTP and RTSP protocols.


### <span class="section-num">3.4</span> Easy Darwin {#easy-darwin}

[EasyDarwin](http://www.easydarwin.org) 是核心流媒体服务 RTSP 开源流媒体直播服务，高效、稳定、可靠、功能齐全，支持 RTSP 流媒体协议，支持安防行业需要的摄像机流媒体转发功能、支持互联网行业需要的多平台 (PC、Android、IOS) RTSP 直播 (H264/MJPEG/MPEG4、AAC/PCMA/PCMU/G726) 功能，底层 (Select/Epoll网络模型、无锁队列调度)和上层( RESTful 接口、WEB 管理、多平台编译)、关键帧索 引(秒开画面)、远程运维等方面优化，这些都是全代码完全开源的，具体接口调用方法和流程 见:<https://github.com/EasyDarwin/EasyDarwin/tree/master/EasyDarwin>;


### <span class="section-num">3.5</span> Red5 {#red5}

[Red5](https://github.com/Red5/red5-server) is an Open Source Flash Server written in Java that supports:

-   Streaming Video (FLV, F4V, MP4, 3GP)
-   Streaming Audio (MP3, F4A, M4A, AAC)
-   Recording Client Streams (FLV and AVC+AAC in FLV container)
-   Shared Objects
-   Live Stream Publishing
-   Remoting
-   Protocols: RTMP, RTMPT, RTMPS, and RTMPE

Additional features supported via plugin:

-   [WebSocket (ws and wss)](https://github.com/Red5/red5-websocket)
-   [RTSP (From Axis-type cameras)](https://github.com/Red5/red5-rtsp-restreamer)
-   [HLS](https://github.com/Red5/red5-hls-plugin)


### <span class="section-num">3.6</span> Simple RSMP Server {#simple-rsmp-server}

[SRS](https://github.com/ossrs/srs) 定位是运营级的互联网直播服务器集群，追求更好的概念完整性和最简单实现的代码。SRS 提供了丰富的接入方案将 RTMP 流接入 SRS ，包括推送 RTMP 到 SRS、推送 RTSP/UDP/FLV 到 SRS、拉取流到 SRS。SRS 还支持将接入的 RTMP 流进行各种变换，譬如将 RTMP 流转码、转发给其他服务器、转封装成 HTTP-FLV 流、转封装成 HLS、转封装成 HDS、录制成 FLV。SRS 包含支大规模集群如 CDN 业务的关键特性，譬如 RTMP 多级集群、VHOST 虚拟服务器、无中断服务 Reload、HTTP-FLV 集群。此外，SRS 还提供丰富的应用接口，包括 HTTP 回调、安全策略 Security、HTTP API 接口、RTMP 测速。SRS 在源站和 CDN 集群中都得到了广泛的应用 Applications。


## <span class="section-num">4</span> Tools and Framework {#tools-and-framework}


### <span class="section-num">4.1</span> FFmpeg {#ffmpeg}

FFmpeg is the leading multimedia framework, able to decode, encode, transcode, mux, demux, stream, filter and play pretty much anything that humans and machines have created. It supports the most obscure ancient formats up to the cutting edge. No matter if they were designed by some standards committee, the community or a corporation. It is also highly portable: FFmpeg compiles, runs, and passes our testing infrastructure FATE across Linux, Mac OS X, Microsoft Windows, the BSDs, Solaris, etc. under a wide variety of build environments, machine architectures, and configurations.


### <span class="section-num">4.2</span> Audacity {#audacity}

Audacity is an easy-to-use, multi-track audio editor and recorder for Windows, Mac OS X, GNU/Linux and other operating systems.


## <span class="section-num">5</span> 流媒体传输协议 {#流媒体传输协议}


### <span class="section-num">5.1</span> RTP/RTCP {#rtp-rtcp}

-   RTP (Real-time Transport Protocol) 是用于多媒体数据流的传输协议，建立在 UDP 协议之上。
-   RTCP (Real-time Transport Control Protocol) 是 RTP 的姊妹协议，用于为 RTP 媒体流提供信道外控制。主要功能是手机相关媒体链接的统计信息（如传输分组数，分组丢失数，网络延迟等）并定期在流媒体会话参加者之间传输控制数据。


### <span class="section-num">5.2</span> RTSP {#rtsp}

RTSP 协议定义了一对多应用程序如何有效通过 IP 网络传送多媒体数据。RTSP 在体系结构上位于 RTP 和 RTCP 之上，它使用 TCP 或 RTP 完成数据传输。HTTP 与 RTSP 相比，HTTP 传送 HTML，而 RTP 传送的是多媒体数据。HTTP 请求由客户机发出，服务器做出响应；RTSP 可以是双向的，即客户机和服务器都可以发出请求。

RTSP 与 RTP 最大的区别在于：RTSP 是一种双向实时数据传输协议，它允许客户端向服务器端发送请求，如回放、快进、倒退等操作。当然 RTSP 可基于 RTP 来传送数据，还可以选择 TCP、UDP、组播 UDP 等通道来发送数据，具有很好的扩展性。它是一种类似于 HTTP 协议的网络应用协议。


### <span class="section-num">5.3</span> RTMP {#rtmp}

RTMP（Real Time Messaging Protocol）是 Adobe Systems 公司为 Flash 播放器和服务器之间音频、视频和数据传输开发的开放协议。它有三种变种：

-   工作在 TCP 之上的明文协议，使用端口 1935；
-   RTMPT 封装在 HTTP 请求之中，可穿越防火墙；
-   RTMPS 类似 RTMPT，但使用的是 HTTPS 连接；
-   Red5 是一个开源的支持 RTMP 协议的媒体服务器；


### <span class="section-num">5.4</span> HLS {#hls}

HTTP Live Streaming (HLS) 是苹果公司实现的基于 HTTP 的流媒体传输协议，可实现流媒体的直播和点播，主要应用于 IOS 系统。HLS 点播是分段 HTTP 点播，不同在于它的分段非常小。要实现 HLS 点播，重点在于对媒体文件分段，目前有不少开源工具可以使用。

-   Apple 开源 Darwin Media Server 支持 HLS 协议；
-   EasyDarwin 支持 HLS 协议；


## <span class="section-num">6</span> 参考文献 {#参考文献}

1.  Google shaka-player, <https://github.com/google/shaka-player>.
2.  Dash, <https://dashif.org>.
3.  hls.js, <https://github.com/video-dev/hls.js>.
4.  vedio.js, <https://github.com/videojs/video.js>.
5.  streamedian, <https://streamedian.com>.
6.  pearPlayer.js, <https://github.com/leszx1/PearPlayer.js>.
7.  Google Shaka-player 初探，<http://www.jackpu.com/gu-ge-kai-yuan-h5-liu-mei-ti-bo-fang-qi-shaka-player-chu-tan/>.
8.  jPlayer, <http://www.jplayer.org>.
9.  Live555 media server,<http://live555.com/mediaServer/>.
10. Ffmpeg.org, <http://ffmpeg.org>.
11. Darwin Streaming Server, <https://macosforge.github.io/dss/>.
12. EasyDarwin, <http://www.easydarwin.org>.
13. Red5 Server, <https://github.com/Red5>.
14. 基于 Red5 的流媒体服务器的搭建和应用,  <https://www.ibm.com/developerworks/cn/opensource/os-cn-Red5/>.
15. VLC, <http://www.videolan.org/vlc/>.
16. 流媒体传输协议 (rtp/rtcp/rtsp/rtmp/mms/hls), <https://blog.csdn.net/DaveBobo/article/details/51125334>.
17. Http Live Streaming, <https://en.wikipedia.org/wiki/HTTP%5FLive%5FStreaming>.
18. HTML5 Video, <https://www.html5rocks.com/en/tutorials/video/basics/>.
19. Media Source Extensions, <http://w3c.github.io/media-source/>.
20. Webrtc, <https://webrtc.org>.
21. HLS, <https://developer.apple.com/streaming/>.
22. Audacity, <https://www.audacityteam.org/download/>.
23. AudioJs, <http://jedfoster.com/AudioJS/>.
24. Audio.js, <https://github.com/kolber/audiojs>.
25. nginx-rtmp-module直播实验, <https://www.jianshu.com/p/3e3cb0870baa>.
