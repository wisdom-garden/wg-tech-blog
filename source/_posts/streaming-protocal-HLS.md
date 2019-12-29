---
uuid: 04e1d470-2a5f-11ea-a6db-d1b16b08ebb8
title: 流媒体协议-HLS
s: 流媒体协议-HLS
date: 2019-12-30 01:16:58
tags:
categories:
coauthor: chenxiaozheng
---

#### 简介
HLS（HTTP Live Streaming）, 是由Apple公司实现的基于HTTP的媒体流传输协议。Apple的全系列产品支持，由于HLS是苹果提出的，所以在Apple的全系列产品包括iphone、ipad、safari都不需要安装任何插件就可以原生支持播放HLS，现在Android也加入了对 HLS 的支持。但PC端目前除了MicrosoftEdge外,Chrome、Firefox等浏览器均不支持该协议的播放,需要特定的html播放器支持，比如常见的videojs。HLS原理非常简单，通过将整个流切割成一个个小的可以通过HTTP下载的媒体文件，然后提供一个配套的媒体列表文件给客户端，让客户端顺序地拉取这些媒体文件, 来实现看上去是在播放一个流的效果。HLS 目前广泛地应用于点播和直播领域。
HLS协议规定：

* 视频的封装格式是TS。
* 视频的编码格式为H264,音频编码格式为MP3、AAC或者AC-3。
* 除了TS视频文件本身，还定义了用来控制播放的m3u8文件（文本文件）。

#### 优点
* 支持直播、时移和点播
* 同一个视频支持多个码率的码流
* 支持根据网络带宽情况自动切换码率码流
* 支持视频加密和用户身份认证
* 采用索引文件与分片的方式伪直播流但很适合CDN等缓存处理

#### 缺点
* 相比RTMP这类长连接协议，延时较高，难以用到互动直播场景。
* 对于点播服务来说，由于TS切片通常较小，海量碎片在文件分发、一致性缓存、存储等方面都有较大挑战。
`说明：HLS理论上延时=1个切片的时长+ 0～1个td(td是EXT-X-TARGETDURATION，可简单理解为播放器取片的间隔时间) + 0～n个启动切片(苹果官方建议是请求到3个片之后才开始播放) + 播放器最开始请求的片的网络延时（网络连接耗时)。
从延时组成公式可以看出，HLS的延时主要是以下四部分组成：
服务器端的编码器和流分割器生成TS文件的时常，HLS协议应用于直播视频传输时，是将媒体文件切割成了对应于媒体分段的TS文件。
播放器取片的间隔时间，在客户端开始下载之前，必须等待服务器端的编码器和流分割器至少生成一个TS 文件。
客户端下载切片的时间及启动播放所需的切片个数，通常下载完两个媒体文件后才能保证不同分段音视频间的无缝连接。
客户端最开始解码并开始播放的时间。
`

#### 架构
![71992c67c69f79f7acf8e019914b5f6c.png](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/StreamingMediaGuide/art/transport_stream_2x.png)

解释一下这张图，从左到右讲，左下方的inputs的视频源是什么格式都无所谓，他与server之间的通信协议也可以任意（比如RTMP），总之只要把视频数据传输到服务器上即可。这个视频在server服务器上被转换成HLS格式的视频（既TS和m3u8文件）文件。细拆分来看server里面的Media encoder的是一个转码模块负责将视频源中的视频数据转码到目标编码格式（H264）的视频数据，视频源的编码格式可以是任何的视频编码格式。转码成H264视频数据之后，在stream segmenter模块将视频切片，切片的结果就是index file（m3u8）和ts文件了。图中的Distribution其实只是一个普通的HTTP文件服务器，然后客户端只需要访问一级index文件的路径就会自动播放HLS视频流了。([官方解释](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/StreamingMediaGuide/HTTPStreamingArchitecture/HTTPStreamingArchitecture.html#//apple_ref/doc/uid/TP40008332-CH101-SW4))

#### 协议详解
HLS 协议的主要内容是关于 M3U8 这个文本协议，其实生成与解析都非常简单，示例如下：
```
#EXT-X-VERSION:3
#EXTM3U
#EXT-X-TARGETDURATION:10
#EXT-X-MEDIA-SEQUENCE:1

#EXTINF:10,
http://media.example.com/segment0.ts
 
#EXTINF:10.0,
http://media.example.com/segment1.ts

#EXTINF:9.5,
http://media.example.com/segment2.ts
#EXT-X-ENDLIST
```

##### Playlist
* HLS通过URI指向的一个Playlist来表示一个媒体流.
* 一个Playlist可以是一个Media Playlist或者Master Playlist,使用UTF-8编码的文本文件, 包含一些 URI 跟描述性的 tags。
* 一个Media Playlist包含一个Media Segments列表,当顺序播放时, 能播放整个完整的流。
* 要想播放这个Playlist,客户端需要首先下载他, 然后播放里面的每一个Media Segment。
* 更加复杂的情况是,Playlist是一个Master Playlist,包含一个Variant Stream集合, 通常每个Variant Stream 里面是同一个流的多个不同版本(如: 分辨率, 码率不同) (参见：[Stream Alternates ](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/StreamingMediaGuide/UsingHTTPLiveStreaming/UsingHTTPLiveStreaming.html#//apple_ref/doc/uid/TP40008332-CH102-SW18))

##### Media Segments
* 每一个 Media Segment 通过一个 URI 指定, 可能包含一个 byte range.
* 每一个 Media Segment 的 duration 通过 EXTINF tag 指定.
* 每一个 Media Segment 有一个唯一的整数 Media Segment Number.
* 有些媒体格式需要一个 format-specific sequence 来初始化一个 parser, 在 Media Segment 被 parse 之前. 这个字段叫做 Media Initialization Section, 通过 EXT-X-MAP tag 来指定.

#### Tags说明
##### Basic Tags
Basic Tags 可以用在 Media Playlist 和 Master Playlist 里面.
```
EXTM3U: 必须在文件的第一行, 标识是一个 Extended M3U Playlist 文件.
EXT-X-VERSION: 表示 Playlist 兼容的版本.
```
##### Media Segment Tags
每一个 Media Segment 通过一系列的 Media Segment tags 跟一个 URI 来指定. 有的 Media Segment tags 只应用与下一个 segment, 有的则是应用所有下面的 segments. 一个 Media Segment tag 只能出现在 Media Playlist 里面.
```
EXTINF: 用于指定 Media Segment 的 duration
EXT-X-BYTERANGE: 用于指定 URI 的 sub-range
EXT-X-DISCONTINUITY: 表示不连续.
EXT-X-KEY: 表示 Media Segment 已加密, 该值用于解密.
EXT-X-MAP: 用于指定 Media Initialization Section.
EXT-X-PROGRAM-DATE-TIME: 和 Media Segment 的第一个 sample 一起来确定时间戳.
EXT-X-DATERANGE: 将一个时间范围和一组属性键值对结合到一起.
```

##### Media Playlist Tags
Media Playlist tags 描述 Media Playlist 的全局参数. 同样地, Media Playlist tags 只能出现在 Media Playlist 里面.
```
EXT-X-TARGETDURATION: 用于指定最大的 Media Segment duration.
EXT-X-MEDIA-SEQUENCE: 用于指定第一个 Media Segment 的 Media Sequence Number.
EXT-X-DISCONTINUITY-SEQUENCE: 用于不同 Variant Stream 之间同步.
EXT-X-ENDLIST: 表示结束.
EXT-X-PLAYLIST-TYPE: 可选, 指定整个 Playlist 的类型.
EXT-X-I-FRAMES-ONLY: 表示每个 Media Segment 描述一个单一的 I-frame.
```

##### Master Playlist Tags
Master Playlist tags 定义 Variant Streams, Renditions 和 其他显示的全局参数. Master Playlist tags 只能出现在 Master Playlist 中.
```
EXT-X-MEDIA: 用于关联同一个内容的多个 Media Playlist 的多种 renditions.
EXT-X-STREAM-INF: 用于指定一个 Variant Stream.
EXT-X-I-FRAME-STREAM-INF: 用于指定一个 Media Playlist 包含媒体的 I-frames.
EXT-X-SESSION-DATA: 存放一些 session 数据.
EXT-X-SESSION-KEY: 用于解密.
```

###### Media or Master Playlist Tags
这里的 tags 可以出现在 Media Playlist 或者 Master Playlist 中. 但是如果同时出现在同一个 Master Playlist 和 Media Playlist 中时, 必须为相同值.
```
EXT-X-INDEPENDENT-SEGMENTS: 表示每个 Media Segment 可以独立解码.
EXT-X-START: 标识一个优选的点来播放这个 Playlist.
```
