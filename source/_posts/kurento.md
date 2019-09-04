---
title: Kurento简介
categories: 
tags: WebRTC
copyright: true
comments: true
description: 
date: 2019-04-02
thumbnail: /pic/pexels-photo-2406454.jpeg
disqusId: Kurento简介
---

---

Kurento是一个WebRTC媒体服务器，同时提供了一系列的客户端API，可以简化供浏览器、移动平台使用的视频类应用程序的开发。

---
<!-- more -->

Kurento是一个WebRTC媒体服务器，同时提供了一系列的客户端API，可以简化供浏览器、移动平台使用的视频类应用程序的开发。Kurento支持：

群组通信（group communications）
媒体流的转码（transcoding）、录制（recording）、广播（broadcasting）、路由（routing）
高级媒体处理特性，包括：机器视觉（CV）、视频索引、增强现实（AR）、语音分析
Kurento的模块化架构使其与第三方媒体处理算法 —— 语音识别、人脸识别 —— 很容易集成。

### 架构

和大部分多媒体通信技术一样，Kurento应用的整体架构包含两个层（layer）或者叫平面（plane）：

- 信号平面（Signaling Plane）：负责通信的管理，例如媒体协商、QoS、呼叫建立、身份验证等
 
- 媒体平面（Media Plane）：负责媒体传输、编解码等

典型Kurento应用的整体架构如下图：

![avatar](https://gmem.site/wp-content/uploads/2017/08/kurentoapp-architecture.png)

### 分层视角

按分层的方式来划分，Kurento应用可以分为三层（类似于典型的Web应用）：

1. 展现层 —— 浏览器、移动应用、其它媒体源等应用客户端：
  1. 基于任意协议和应用逻辑层通信，发起信号处理
  2. 基于RTP/HTTP/WebRTC协议和KMS通信：
     1. 通过KMS的输入端点，传输媒体流到KMS
     2. 通过KMS的输出端点，从KMS获得媒体流
2. 应用逻辑层——应用服务器负责信号平面：
  1. 基于WebSocket/HTTP/REST/SIP等方式和应用客户端通信，进行信号处理
  2. 内嵌Kurento Client，基于Kurento Protocol与KMS通信，管理媒体元素/媒体管线
3. 服务层——KMS负责媒体平面，可以对输入流进行各种处理，并产生输出流

### 层之间的交互

媒体协商（信号处理）阶段：

1. 客户端首先向应服务器请求某种媒体特性（例如请求一个九画面视频监控流、请求发布自己的SDP）。这块WebRTC没有规定，可以基于任何协议（HTTP/WS/SIP）实现
2. 应用服务器接收到请求后，执行特定的服务器端逻辑，例如AAA（认证授权审计）、CDR生成等
3. 应用服务器处理请求，并命令KMS实例化适当的媒体元素、构建媒体流（例如从多个RTSP源混合出九画面）
4. 媒体流构建完毕后，KMS应答应用服务器，后者应答客户端，告知其如何获取媒体服务

媒体交换阶段：

1. 客户端利用协商阶段收集的信息，向KMS发起请求（例如向目标端口发起UDP请求，获取九画面视频监控流）

下图是交互的序列示意，注意先后顺序：
![avatar](https://gmem.site/wp-content/uploads/2017/08/kurentoapp-generic_interactions.png)

### WebRTC应用的例子

Kurento允许基于WebRTC建立浏览器和KMS之间的实时多媒体会话：

1. 客户端基于SDP来发布自己的媒体特性，请求发送给应用服务器
2. 应用服务器根据SDP来创建合适的WebRTC端点，并请求KMS生成一个响应SDP
3. 应用服务器获得响应SDP后，将其返回给客户端
4. 由于双方都知道对方的SDP了，客户端和KMS可以进行媒体交换了

下图是交互的序列示意：

![avatar](https://gmem.site/wp-content/uploads/2017/08/kurento-webrtc-session.png)

Kurento也可以作为一个媒体代理，让浏览器之间建立直接的媒体交换。交互序列仍然如上图，仅仅是KMS返回的SDP不同

### 媒体服务器

WebRTC让浏览器能够进行实时的点对点通信（在没有服务器的情况下）。但是要想实现群组通信、媒体流录制、媒体广播、转码等高级特性，没有媒体服务器是很难实现的。

Kurento的核心是一个媒体服务器（Kurento Media Server，KMS），负责媒体的传输、处理、加载、录制，主要基于 GStreamer实现。此媒体服务器的特性包括：

1. 网络流协议处理，包括HTTP、RTP、WebRTC
2. 支持媒体混合（mixing）、路由和分发的群组通信（MCU、SFU功能）
3. 对机器视觉和增强现实过滤器的一般性支持
4. 媒体存储支持，支持对WebM、MP4进行录像操作，可以播放任何GStreamer支持的视频格式
5. 对于GStreamer支持的编码格式，可以进行任意的转码，例如VP8, H.264, H.263, AMR, OPUS, Speex, G.711

### 模块

KMS基于模块化的设计，模块主要分为三类：

1. 核心（kms-core）
2. 媒体元素（kms-elements）
3. 过滤器（kms-filters）

其它增强KMS的模块，例如kms-crowddetector, kms-pointerdetector, kms-chroma, kms-platedetector KMS允许用户扩展自己的模块。

### 协议

Kurento Protocol是一个网络协议，通过WebSocket暴露KMS的特性。

Kurento API是对上述协议的OO封装，通过此API能够创建媒体元素和管线。Kurento提供了API的Java、JavaScript绑定。

### 客户端

Kurento提供了Java、JavaScript（包括浏览器和Node.js）的客户端库，通过这些库你可以控制媒体服务器。对于其它编程语言，可以使用 Kurento Protocol协议（基于WebSocket/JSON-RPC）。

Kurento客户端API基于所谓媒体元素（Media Element）的概念。一个每天元素持有一种特定的媒体特性。例如：

1. 媒体元素WebRtcEndpoint的特性是，接收WebRTC媒体流
2. 媒体元素RecorderEndpoint的特性是，将接收到的媒体流录制到文件系统
3. 媒体元素FaceOverlayFilter则能够检测人脸，在其上方显示一个特定的图像
开箱即用的媒体元素如下图：

![avatar](https://gmem.site/wp-content/uploads/2017/08/kurento-basic-toolbox.png)

从开发者角度来说，操控媒体元素就好像搭积木。 你只需要按照期望的拓扑结构把它们连接起来就可以了。一系列连接起来的媒体元素称为媒体管线（Media Pipeline）。只有一个管线内部的媒体元素才能相互通信

当创建管道时，开发者需要明确希望使用到的特性，以及媒体连接（connectivity） 产生媒体的元素和消费媒体的元素之间的连接：

```

sourceMediaElement.connect(sinkMediaElement);
// 例如：客户端接收WebRTC流并录制到媒体服务器的文件系统
webRtcEndpoint.connect(recorderEndpoint);

```

### Web客户端

为了简化浏览器客户端的WebRTC流处理，Kurento提供了工具WebRtcPeer，你仍然可以使用WebRTC的标准API，以及连接到WebRtcEndpoint。