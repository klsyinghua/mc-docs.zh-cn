---
title: Azure 媒体服务 - 实时传送视频流中的超时元数据信号 | Microsoft Docs
description: 本规范概述在将计时元数据引入和流式传输到 Azure 媒体服务时使用的信号发送法。 这包括对泛型计时元数据信号 (ID3) 的支持，以及对用于广告插入和接合信号的 SCTE-35 信号的支持。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 08/22/2019
ms.date: 09/23/2019
ms.author: v-jay
ms.openlocfilehash: e636d135a4bdeda5da49ce126ba0176bca8fcfc5
ms.sourcegitcommit: 8248259e4c3947aa0658ad6c28f54988a8aeebf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124366"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>实时传送视频流中的超时元数据信号 

上次更新时间：2019-08-22

### <a name="conformance-notation"></a>一致表示法

本文档中的关键字“必须”、“不得”、“需要”、“应”、“不应”、“应该”、“不能”、“建议”、“可以”和“可选”根据 RFC 2119 中所述予以解释

## <a name="1-introduction"></a>1.简介 

为了表示客户端播放器上的广告插入或自定义元数据事件，广播装置通常使用视频中嵌入的计时元数据。 为实现这些方案，媒体服务将提供对计时元数据以及从实时传送视频流频道的引入点到客户端应用程序的传输支持。
本规范针对实时传送视频流信号中的计时元数据概述了媒体服务支持的多种模式：

1. 符合 [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] 和 [RFC8216] 所述标准的 [SCTE-35] 信号

2. 符合 RTMP 广告信号旧版 [Adobe-Primetime] 规范的 [SCTE-35] 信号。
   
3. 适用于**非** [SCTE-35] 且可以携带 [ID3v2] 或应用程序开发人员定义的其他自定义架构的泛型计时元数据信号模式。

## <a name="11-terms-used"></a>1.1 所用术语

| 术语              | 定义 |
|-------------------|------------|
| 广告中断          | 可以安排交付一个或多个广告的位置或时间点；与收益和放置机会相同。 |
| 广告决策服务| 确定要向用户显示哪些广告及其持续时间的外部服务。 该服务通常由合作伙伴提供，不在本文档的介绍范围内。|
| 提示               | 指示即将发生的广告中断的时间和参数。 请注意，提示可以指示广告中断的等待开关、广告中断中下一广告的等待开关，以及从广告中断到主要内容的等待开关。 |
| 打包器          | Azure 媒体服务“流式处理终结点”提供适用于 DASH 和 HLS 的动态打包功能，在媒体行业称为“打包器”。 
| 呈现时间 | 事件呈现给查看者的时间。 该时间表示媒体时间线上查看者可以看到事件的那一刻。 例如，SCTE-35 splice_info() 命令消息的呈现时间为 splice_time()。 |
| 到达时间      | 事件消息到达的时间。 该时间通常不同于事件的呈现时间，因为事件消息在事件的呈现时间之前发送。                                     |
| 稀疏轨道      | 不连续的媒体轨迹，时间与父级或控件轨迹的时间同步。                                                                                                                                    |
| 源            | Azure 媒体流式处理服务                                                                                                                                                                                                |
| 信道接收器      | Azure 媒体实时传送视频流服务                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming 协议                                                                                                                                                                                               |
| DASH              | 通过 HTTP 的动态自适应流式处理                                                                                                                                                                                             |
| 平滑            | 平滑流式处理协议                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG 2 传输流                                                                                                                                                                                                         |
| RTMP              | 实时多媒体协议                                                                                                                                                                                                    |
| uimsbf            | 无符号整数，最高有效位优先。                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1.2 规范参考

以下文档包含本文中参考的条款，构成了本文档的条款。 标准主体随时可修订所有文档，我们建议读者调查应用下面所列最新版本文档的可能性。 另外提醒读者，新版参考文档可能与 Azure 媒体服务的此计时元数据规范版本不兼容。


|标准  |定义  |
|---------|---------|
|[Adobe-Primetime] | [Primetime 数字节目插入信号规范 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [FLASH ActionScript 语言参考](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | [“操作消息格式 AMF0”](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [DASH-IF-IOP]     | DASH 行业论坛互操作指南 v4.2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | HTTP Live Streaming 的计时元数据 - [https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [CMAF-ID3]         | [常用媒体应用程序格式 (CMAF) 中的计时元数据](https://aomediacodec.github.io/av1-id3/)
| [ID3v2]           | ID3 标记版本 2.4.0 [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12：第 12 部分：ISO 基础媒体文件格式，2012-07-15 第 4 版  |
| [MPEGDASH]        | 信息技术 - 通过 HTTP 的动态自适应流式处理 (DASH) - 第 1 部分：媒体展现说明和分段格式。 2014 年 5 月。 已发布。 URL： https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | 信息技术 - 多媒体应用程序格式 (MPEG-A) -- 第 19 部分：分段媒体的常用媒体应用程序格式 (CMAF)。 2018 年 1 月。 已发布。 URL： https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 信息技术 -- MPEG 系统技术 -- 第 7 部分：ISO 基本媒体格式文件中的通用加密。 2016 年 2 月。 已发布。 URL： https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | [“Microsoft 平滑流式处理协议”，2014 年 5 月 15 日](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-Ingest]  | [Azure 媒体服务分片 MP4 实时引入规范](/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP Live Streaming。 2017 年 8 月。 信息。 [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |Base16、Base32 和 Base64 数据编码 - [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| [RTMP]            |[“Adobe 实时消息传送协议”，2012 年 12 月 21 日](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35：2019 - 有线电视数字节目插入提示消息 - https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – 基于 IP 的有线电视服务的 MPEG DASH 第 1 部分：MPD 约束和扩展 |
| [SCTE-214-3]      | SCTE 214-3 2015 – 基于 IP 的有线电视服务的 MPEG DASH 第 3 部分：DASH/FF 配置文件 |
| [SCTE-224]        | SCTE 224 2018r1 – 事件计划和通知接口 |
| [SCTE-250]        | 事件和信号管理 API (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2.计时元数据引入

Azure 媒体服务支持 [RTMP] 和平滑流式处理 [MS-SSTR-Ingest] 协议的实时带内元数据。 可以结合自己独特的自定义架构（JSON、二进制、XML）和行业定义的格式（例如 ID3，或广播流中广告信号的 SCTE-35）使用实时元数据来自定义事件。 

本文提供有关如何使用 Azure 媒体服务支持的引入协议发送自定义计时元数据信号的详细信息。 本文还介绍了如何使用计时元数据信号修饰 HLS、DASH 和平滑流的清单，以及如何在使用 CMAF（MP4 片段）或 HLS 的传输流 (TS) 段传输内容时在带内承载计时元数据。 

计时元数据的常见用例方案包括：

 - 用于在直播活动或线性广播中触发广告中断的 SCTE-35 广告信号
 - 可在客户端应用程序（浏览器、iOS 或 Android）中触发事件的自定义 ID3 元数据
 - 用于在客户端应用程序中触发事件的自定义 JSON、二进制或 XML 元数据
 - 来自实时编码器、IP 摄像头或无人机的遥测数据
 - 来自运动、人脸检测等 IP 摄像头的事件
 - 来自动作摄像头、无人机或运动设备的地理位置信息
 - 歌词
 - 线性实时源中的节目边界
 - 要在实时源中显示的图像或扩充元数据
 - 赛事比分或比赛计时信息
 - 要连同浏览器中的视频一起显示的交互式广告包
 - 知识竞赛或投票
  
Azure 媒体服务实时事件和打包器可以接收这些计时元数据信号，并将其转换成可通过基于标准的协议（例如 HLS 和 DASH）抵达客户端应用程序的元数据流。


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 计时元数据

[RTMP] 协议允许在各种方案（包括自定义元数据和 SCTE-35 广告信号）中发送计时元数据信号。 

广告信号（提示消息）以嵌入在 [RTMP] 流中的 [AMF0] 提示消息的形式发送。 提示消息可能会在实际事件前的一段时间发送，否则需要发生 [SCTE35] 广告接合信号。 为支持此方案，将在提示消息内发送事件的实际呈现时间戳。 有关详细信息，请参见 [AMF0]。

用于引入 RTMP 的 Azure 媒体服务支持以下 [AMF0] 命令：

- **onUserDataEvent** - 用于自定义元数据或 [ID3v2] 计时元数据
- **onAdCue** - 主要用于在实时流中发出广告定位机会的信号。 支持两种形式的提示：简单模式和“SCTE-35”模式。 
- **onCuePoint** - 受某些本地硬件编码器（例如 Elemental 实时编码器）的支持，用于发出 [SCTE35] 消息信号。 
  

下表介绍媒体服务将为“简单”和 [SCTE35] 消息模式引入的 AMF 消息有效负载的格式。

可以使用 [AMF0] 消息的名称来区分相同类型的多个事件流。  对于 [SCTE-35] 消息和“简单”模式，AMF 消息的名称在 [Adobe-Primetime] 规范中必须是“onAdCue”。  下面未列出的任何字段在引入时应由 Azure 媒体服务忽略。

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 使用“onUserDataEvent”和 RTMP 提供自定义元数据

若要使用 RTMP 协议从上游编码器、IP 摄像头、无人机或设备提供自定义元数据源，请使用“onUserDataEvent”[AMF0] 数据消息命令类型。

**“onUserDataEvent”** 数据消息命令必须携带一个消息有效负载，以及要由媒体服务捕获的、打包为带内文件格式的以下定义，此外还要携带 HLS、DASH 和平滑流的清单。
建议发送计时元数据消息的频率不要超过每 0.5 秒（500 毫秒）一次，否则实时流的稳定性可能会出现问题。 如果需要提供帧级元数据，每个消息可以聚合多个帧的元数据。 如果发送多比特率流，建议另外提供单比特率中的元数据，目的只是减少带宽，并避免视频/音频处理受到干扰。 

**“onUserDataEvent”** 的有效负载应是 [MPEGDASH] EventStream XML 格式消息。 这样，就可以轻松地传入自定义的架构，这些架构可以在通过 HLS 或 DASH 协议传送的 CMAF [MPEGCMAF] 内容的带内“emsg”有效负载中携带。 每个 DASH 事件流消息包含一个充当 URN 消息方案标识符并定义消息有效负载的 schemeIdUri。 为实现互操作性，行业协议已标准化某些方案，例如，适用于 [ID3v2] 的“https://aomedia.org/emsg/ID3”，或适用于 [SCTE-35] 的 **urn:scte:scte35:2013:bin**。 任何应用程序提供程序都可以使用其控制的 URL（拥有的域）定义自身的自定义方案，并可以在该 URL 中提供规范（如果已选择）。 如果播放器具有已定义方案的处理程序，则只需提供此组件即可识别有效负载和协议。

[MPEG-DASH] EventStream XML 有效负载的架构定义为（摘自 DASH ISO-IEC-23009-1-3rd Edition）。 请注意，目前对于每个“EventStream”仅支持一个“EventType”。 如果在 **EventStream** 中提供了多个事件，只会处理第一个 **Event** 元素。

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>使用 ID3 架构 ID 和 base64 编码数据有效负载的示例 XML 事件流。  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>使用自定义架构 ID 和 base64 编码二进制数据的示例事件流  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>使用自定义架构 ID 和自定义 JSON 的示例事件流  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>内置支持的方案 ID URI
| 方案 ID URI                 |  说明                                             |
|-------------------------------|----------------------------------------------------------|
| https:\//aomedia.org/emsg/ID3   | 描述如何在 CMAF 兼容的 [MPEGCMAF] 分段 MP4 中以计时元数据的形式携带 [ID3v2] 元数据。 有关详细信息，请参阅[常用媒体应用程序格式 (CMAF) 中的计时元数据](https://aomediacodec.github.io/av1-id3/) |

### <a name="event-processing-and-manifest-signaling"></a>事件处理和清单信号

收到有效的 **“onUserDataEvent”** 事件后，Azure 媒体服务将查找与 EventStreamType（在 [MPEGDASH] 中定义）匹配的有效 XML 有效负载，分析该 XML 有效负载，然后将其转换为 [MPEGCMAF] MP4 片段“emsg”版本 1，以便存储在实时存档中并传输到媒体服务打包器。   打包器将检测实时流中的“emsg”块并执行以下操作：

- (a) 将其“动态打包”成 TS 段，以传送到符合 HLS 计时元数据规范 [HLS-TMD] 的 HLS 客户端，或者
- (b) 传递该块，以通过 HLS 或 DASH 在 CMAF 片段中传送，或者 
- (c) 将其转换为稀疏轨迹信号，以通过平滑流式处理 [MS-SSTR] 进行传送。

除了带内“emsg”格式的 CMAF 或适用于 HLS 的 TS PES 数据包外，适用于 DASH (MPD) 和平滑流的清单将包含对带内事件流的引用（在平滑流中也称为“稀疏流轨迹”）。 

单个事件或其数据有效负载不会直接在 HLS、DASH 或平滑流清单中输出。 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>onUserDataEvent 事件的其他信息性约束和默认值

- 如果未在 EventStream 元素中设置时间刻度，默认将使用 RTMP 1 kHz 时间刻度
- onUserDataEvent 消息的最大传送频率限制为每 500 毫秒一次。如果更频繁地发送事件，可能会影响带宽和实时源的稳定性

## <a name="212-rtmp-ad-cue-signaling-with-oncuepoint"></a>2.1.2 使用“onCuePoint”和 RTMP 发送广告提示信号

Azure 媒体服务可以侦听和响应可用于在实时流中通知各种实时同步元数据的多个 [AMF0] 消息类型。  [Primetime] 规范定义了两个称为“简单”和“SCTE-35”模式的提示类型。 对于“简单”模式，媒体服务支持称为“onAdCue”的单个 AMF 提示消息，该消息使用与下表中的“简单模式”信号定义匹配的有效负载。  

以下部分显示了 RTMP“简单”模式有效负载，它可用于发送基本“spliceOut”广告信号，该信号将传递到 HLS、DASH 和 Microsoft 平滑流式处理的客户端清单。 这对于以下场景非常有用：客户没有基于 SCTE-35 的复杂广告信号部署或插入系统，而是使用基本的本地编码器通过 API 在提示消息中发送。 通常，本地编码器支持使用基于 REST 的 API 来触发此信号，这还会通过将 IDR 帧插入视频并启动新的 GOP 来对视频流进行“拼接适配”。

## <a name="213--rtmp-ad-cue-signaling-with-oncuepoint---simple-mode"></a>2.1.3 使用“onCuePoint”和 RTMP 发送广告提示信号 - 简单模式

| 字段名称 | 字段类型 | 必需？ | 说明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必须 | 事件消息。  应为“SpliceOut”以指定简单模式接合。                                              |
| id         | String     | 必须 | 描述接合或片段的唯一标识符。 标识消息的此实例                            |
| duration   | Number     | 必须 | 接合持续时间。 单位为小数形式的秒。                                                                |
| elapsed    | Number     | 可选 | 如果信号重复出现以便支持接收，此字段应为自接合开始后已经过去的呈现时间量。 单位为小数形式的秒。 使用简单模式时，此值不得超过接合的原始持续时间。                                                  |
| time       | Number     | 必须 | 呈现时间应为接合的时间。 单位为小数形式的秒。                                     |

---
 
## <a name="214-rtmp-ad-cue-signaling-with-oncuepoint---scte-35-mode"></a>2.1.4 使用“onCuePoint”和 RTMP 发送广告提示信号 - SCTE-35 模式

使用需要将完整 SCTE-35 有效负载消息传递到 HLS 或 DASH 清单的更高级广播生产工作流时，最好是使用 [Adobe-Primetime] 规范的“SCTE-35 模式”。  此模式支持将带内 SCTE-35 信号直接发送到本地实时编码器，然后使用 [Adobe-Primetime] 规范中指定的“SCTE-35 模式”将信号编码成 RTMP 流。 

通常，SCTE-35 消息只能显示在本地编码器上的 MPEG-2 传输流 (TS) 输入中。 有关如何配置包含 SCTE-35 的传输流引入，以及如何在 Adobe SCTE-35 模式下将其传递到 RTMP 的详细信息，请咨询编码器制造商。

在此方案中，必须使用 **“onAdCue”** [AMF0] 消息类型从本地编码器发送以下有效负载。

| 字段名称 | 字段类型 | 必需？ | 说明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必须 | 事件消息。  对于 [SCTE-35] 消息，这必须是 base64 编码的 [RFC4648] 二进制 splice_info_section()，才能将消息发送到 HLS、Smooth 和 Dash 客户端。                                              |
| type       | String     | 必须 | 标识消息方案的 URN 或 URL。 对于 [SCTE-35] 消息，这**应该**是 **“scte35”** ，才能根据 [Adobe-Primetime] 将消息发送到 HLS、Smooth 和 Dash 客户端。 也可以选择性地使用 URN“urn:scte:scte35:2013:bin”发出 [SCTE-35] 消息信号。 |
| id         | String     | 必须 | 描述接合或片段的唯一标识符。 标识消息的此实例。  具有等效语义的消息应具有相同的值。|
| duration   | Number     | 必须 | 如果已知，则为事件或广告接合段的持续时间。 如果未知，该值**应该**为 0。                                                                 |
| elapsed    | Number     | 可选 | 如果 [SCTE-35] 广告信号重复出现以便接收，此字段应为自接合开始后已经过去的呈现时间量。 单位为小数形式的秒。 在 [SCTE-35] 模式下，此值可能超过接合或片段的最初指定持续时间。                                                  |
| time       | Number     | 必须 | 事件或广告接合的呈现时间。  按照 [ISO-14496-12] Annex I 中的定义，呈现时间和持续时间**应该**与类型 1 或 2 的流访问点保持一致。对于 HLS 出口，时间和持续时间**应该**与片段边界保持一致。 相同事件流中的不同事件消息的呈现时间和持续时间不得重叠。 单位为小数形式的秒。

---
## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 使用 Elemental Live“onCuePoint”和 RTMP 发送广告信号

Elemental Live 本地编码器支持在 RTMP 信号中使用广告标记。 Azure 媒体服务目前仅支持 RTMP 的“onCuePoint”广告标记类型。  可以通过 Elemental Media Live 编码器中的“Adobe RTMP 组设置”，或者通过在 API 中将“**ad_markers**”设置为“onCuePoint”，来启用此功能。  有关详细信息，请参阅 Elemental Live 文档。 在 RTMP 组中启用此功能会将 SCTE-35 信号传递到 Adobe RTMP 输出，以供 Azure 媒体服务处理。

“onCuePoint”消息类型在 [Adobe-Flash-AS] 中定义，从 Elemental Live RTMP 输出发送时采用以下有效负载结构。


| 属性  |说明  |
|---------|---------|
|  name     | 在 Elemental Live 中的名称应为“**scte35**”。 |
|time     |  时间线中提示点在视频文件中发生的时间（秒） |
| type     | 提示点的类型应设置为“**event**”。 |
| 参数 | 名称/值对字符串的关联数组，包含 SCTE-35 消息中的信息，包括 ID 和持续时间。 这些值由 Azure 媒体服务分析，将包含在清单修饰标记中。  |


使用此广告标记模式时，HLS 清单输出类似于 Adobe 的“简单”模式。 

### <a name="216-cancellation-and-updates"></a>2.1.6 取消和更新

可以通过发送多条具有相同呈现时间和 ID 的消息来取消或更新消息。 呈现时间和 ID 用于唯一标识事件，并且针对具体呈现时间收到的满足前置式约束的最后一条消息为待处理的消息。 已更新的事件将替换以前接收的任何消息。 前置式约束为 4 秒。 在呈现时间前的至少 4 秒内收到的消息将有待处理。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 分片 MP4 引入（平滑流式处理）

有关实时流引入的要求，请参阅 [MS-SSTR-Ingest]。 以下各节提供有关引入超时呈现元数据的详细信息。  超时呈现元数据作为稀疏轨迹引入，Live Server Manifest Box（请参阅 MS-SSTR）和 Movie Box（“moov”）中都对稀疏轨迹进行了定义。  

每个稀疏片段都包含 Movie Fragment Box（“moof”）和 Media Data Box（“mdat”），其中“mdat”块是二进制消息。

为了实现帧准确的广告插入，编码器必须在显示需要插入提示时拆分片段。  必须创建以新建 IDR 帧开头的新片段，或使用 [ISO-14496-12] 附录 I 中定义的类型 1 或 2 流访问点 (SAP)。这样，Azure 媒体打包器将正确生成 HLS 清单和 DASH 多时段清单，其中的新时段从帧准确的接合适配呈现时间开始。

### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box

稀疏轨迹**必须**在具有 **\<textstream\>** 条目的 Live Server Manifest Box 中进行声明，并且**必须**包含以下属性集：

| **属性名称** | **字段类型** | **必需？** | **说明**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | 必须      | **必须**为“0”，以指示具有未知、可变比特率的轨迹。                                                                                                                                                                                                 |
| parentTrackName    | String         | 必须      | **必须**是父级轨迹的名称，稀疏轨迹时间码与父级轨迹时间刻度一致。 父级轨迹不能为稀疏轨迹。                                                                                                                    |
| manifestOutput     | 布尔        | 必须      | **必须**为“true”，以指示将在 Smooth 客户端清单中嵌入稀疏轨迹。                                                                                                                                                               |
| 子类型            | String         | 必须      | **必须**是四字符代码的“DATA”。                                                                                                                                                                                                                        |
| 方案             | String         | 必须      | **必须**为标识消息方案的 URN 或 URL。 对于 [SCTE-35] 消息，这**必须**为“urn:scte:scte35:2013:bin”，以便按照 [SCTE-35] 将消息发送至 HLS、Smooth 和 Dash 客户端。 |
| trackName          | String         | 必须      | **必须**为稀疏轨迹的名称。trackName 可用来区分具有相同方案的多个事件流。 每个唯一事件流**必须**具有唯一的轨迹名称。                                                                           |
| timescale          | Number         | 可选      | **必须**为父级轨迹时间刻度。                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 Movie Box

作为稀疏轨迹的流标头的一部分，Movie Box（“moov”）将遵循 Live Server Manifest Box。

如 [ISO-14496-12] 中定义的，“moov”块**应该**包含 **TrackHeaderBox (‘tkhd’)** 块，且具有以下约束：

| **字段名称** | **字段类型**          | **必需？** | **说明**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64 位无符号整数 | 必须      | **应该**为 0，因为轨迹块具有零个示例，且轨迹块中示例的总持续时间为 0。 |

---

如 [ISO-14496-12] 中所述，“moov”块**应该**包含 **HandlerBox (‘hdlr’)** ，且具有以下约束：

| **字段名称** | **字段类型**          | **必需？** | **说明**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 位无符号整数 | 必须      | **应该**为“meta”。 |

---

如 [ISO-14496-12] 中所述，“stsd”块**应该**包含带有编码名称的 MetaDataSampleEntry 块。  例如，对于 SCTE-35 消息，编码名称**应该**为“scte”。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box 和 Media Data Box

稀疏轨迹片段包含 Movie Fragment Box（“moof”）和 Media Data Box（“mdat”）。

> [!NOTE]
> 为了实现帧准确的广告插入，编码器必须在显示需要插入提示时拆分片段。  必须创建以新建 IDR 帧开头的，或者以 [ISO-14496-12] 附录 I 中定义的类型 1 或 2 流访问点 (SAP) 开头的新片段。
> 

如 [MS-SSTR] 中定义，MovieFragmentBox（“moof”）框**必须**包含 **TrackFragmentExtendedHeaderBox (‘uuid’)** 框，且具有以下字段：

| **字段名称**         | **字段类型**          | **必需？** | **说明**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 位无符号整数 | 必须      | **必须**为事件的到达时间。 此值可使消息与父级轨迹保持一致。   |
| fragment_duration      | 64 位无符号整数 | 必须      | **必须**为事件的持续时间。 持续时间可以为零，以指示持续时间未知。 |

---


MediaDataBox (‘mdat’) 框**必须**采用以下格式：

| **字段名称**          | **字段类型**                   | **必需？** | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 版本                 | 32 位无符号整数 (uimsbf) | 必须      | 确定“mdat”块的内容格式。 将忽略无法识别的版本。 当前仅支持版本 1。                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 位无符号整数 (uimsbf) | 必须      | 标识消息的此实例。 具有等效语义的消息应具有相同的值；即，处理具有相同 ID 的任何一个事件消息块就足够了。                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 位无符号整数 (uimsbf) | 必须      | TrackFragmentExtendedHeaderBox 中指定的 fragment_absolute_time 总和以及 presentation_time_delta **必须**是事件的呈现时间。 按照 [ISO-14496-12] Annex I 中的定义，呈现时间和持续时间**应该**与类型 1 或 2 的流访问点保持一致。对于 HLS 出口，时间和持续时间**应该**与片段边界保持一致。 相同事件流中的不同事件消息的呈现时间和持续时间**不得**重叠。 |
| message                 | Byte Array                       | 必须      | 事件消息。 对于 [SCTE-35] 消息，消息仍为二进制 binary splice_info_section()。 对于 [SCTE-35] 消息，这**必须**为 splice_info_section()，以便按照 [SCTE-35] 将消息发送至 HLS、Smooth 和 Dash 客户端。 对于 [SCTE-35] 消息，二进制 splice_info_section() 是“mdat”块的有效负载，且它**不是** base64 编码。                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 取消和更新

可以通过发送多条具有相同呈现时间和 ID 的消息来取消或更新消息。  呈现时间和 ID 可唯一标识事件。 针对具体呈现时间收到的满足前置式约束的最后一条消息为待处理的消息。 已更新的消息将替换以前接收的任何消息。  前置式约束为 4 秒。 在呈现时间前的至少 4 秒内收到的消息将有待处理。 


## <a name="3-timed-metadata-delivery"></a>3 超时元数据交付

事件流数据对媒体服务不透明。 媒体服务仅传递引入终结点和客户端终结点之间的三条信息。 根据 [SCTE-35] 和/或客户端的流式处理协议，将向客户端传递以下属性：

1.  架构 - 标识消息方案的 URN 或 URL。
2.  呈现时间 - 媒体时间线上的事件的呈现时间。
3.  持续时间 - 事件的持续时间。
4.  ID - 事件的可选唯一标识符。
5.  消息 - 事件数据。

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft 平滑流式处理清单  

有关如何设置稀疏消息轨迹格式的详细信息，参阅稀疏轨迹处理 [MS-SSTR]。对于 [SCTE35] 消息，平滑流式处理会将 base64 编码的 splice_info_section() 输出到稀疏片段。
StreamIndex **必须**具有子类型“DATA”，CustomAttributes **必须**包含名称为“Schema”、值为“urn:scte:scte35:2013:bin”的属性。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>演示 base64 编码 [SCTE35] splice_info_section() 的平滑客户端清单示例
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS 清单修饰

Azure 媒体服务支持在实时或按需事件期间使用以下 HLS 清单标记来通知广告权益信息。 

- 在 Apple HLS [RFC8216] 中定义的 EXT-X-DATERANGE
- 在 [Adobe-Primetime] 中定义的 EXT-X-CUE - 此模式被视为“传统”模式。 客户应尽可能地采用 EXT-X-DATERANGE 标记。

每个标记的数据输出根据所用的引入信号模式而异。 例如，使用 Adobe 简单模式进行的 RTMP 引入不包含完整的 SCTE-35 base64 编码有效负载。

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 将 Apple HLS 与 Adobe Primetime EXT-X-DATERANGE 配合使用（推荐）

Apple HTTP Live Streaming [RFC8216] 规范允许发送 [SCTE-35] 消息信号。 消息将根据标题为“将 SCTE-35 映射到 EXT-X-DATERANGE”的 [RFC8216] 部分插入到 EXT-X-DATERANGE 标记中的分段播放列表。  客户端应用层可以分析 M3U 播放列表并处理 M3U 标记，或通过 Apple Player Framework 接收事件。  

Azure 媒体服务（版本 3 API）中**建议**的方法是遵循 [RFC8216]，并将 EXT-X_DATERANGE 标记用于清单中的 [SCTE35] 广告权益修饰。

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 将 Apple HLS 与 Adobe Primetime EXT-X-CUE 配合使用（传统方法）

Azure 媒体服务（版本 2 和 3 API）中还提供一种“传统”实现，该实现使用 [Adobe-Primetime]“SCTE-35 模式”中定义的 EXT-X-CUE 标记。 在此模式下，Azure 媒体服务会将 base64 编码的 [SCTE-35] splice_info_section() 嵌入到 EXT-X-CUE 标记中。  

“传统”EXT-X-CUE 标记的定义如下，也可以在 [Adobe-Primetime] 规范中以规范方式引用它。 应该只在有必要时才将它用于传统的 SCTE35 信号，否则应根据 EXT-X-DATERANGE 所述在 [RFC8216] 中定义建议的标记。 

| **属性名称** | **类型**                      | **必需？**                             | **说明**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 提示                | 带引号的字符串                 | 必须                                  | 如 [RFC4648] 中所述编码为 base64 字符串的消息。 对于 [SCTE-35] 消息，这是 base64 编码的 splice_info_section()。                                                                                                |
| 类型               | 带引号的字符串                 | 必须                                  | 标识消息方案的 URN 或 URL。 对于 [SCTE-35] 消息，类型将采用特殊值“scte35”。                                                                                                                                |
| ID                 | 带引号的字符串                 | 必须                                  | 事件的唯一标识符。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。                                                                                                                                          |
| DURATION           | 十进制浮点数 | 必须                                  | 事件持续时间。 如果未知，该值**应该**为 0。 单位为小数形式的秒。                                                                                                                                                                                           |
| 已用时间            | 十进制浮点数 | 可选，但是对于滑动窗口为必需 | 如果信号重复出现以便支持滑动呈现窗口，此字段**必须**为事件开始后已经过去的呈现时间量。 单位为小数形式的秒。 此值可能超过接合或片段的最初指定持续时间。 |
| TIME               | 十进制浮点数 | 必须                                  | 事件的呈现时间。 单位为小数形式的秒。                                                                                                                                                                                                                    |


HLS 播放器应用程序层将使用 TYPE 执行以下操作：标识消息的格式、对消息进行解码、应用必要的时间转换以及处理事件。  根据事件的时间戳，事件时间在父级轨迹的片段播放列表中保持同步。  它们被插入在最近的片段（#EXTINF 标记）前面。

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 使用“传统”Adobe Primetime EXT-X-CUE 的 HLS 分段播放列表示例

以下示例演示使用 Adobe Primetime EXT-X-CUE 标记的 HLS 清单修饰。  “CUE”参数包含完整的 base64 编码 SCTE-35 splice_info 有效负载，表示该信号是在 Adobe SCTE-35 信号模式下使用 RTMP 传入的的，或者是通过平滑流式处理 SCTE-35 信号模式传入的。 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4“传统”Adobe Primetime EXT-X-CUE 的 HLS 消息处理

事件会在每个视频和音频轨迹的片段播放列表中发出信号。根据 [Adobe-Primetime] 的要求，EXT-X-CUE 标记**必须**始终位于第一个 HLS 片段（用于接合结束或片段开始）之前或位于最后一个 HLS 片段（用于接合开始或片段结束）之后，以便其 TIME 和 DURATION 属性进行引用。

根据 [Adobe-Primetime] 的要求，启用滑动呈现窗口后，EXT-X-CUE 标记**必须**重复出现足够多的次数，以便始终在片段播放列表中完整介绍接合或片段，并且 ELASPED 属性**必须**用于指示接合或片段活跃的时间量。

启用滑动呈现窗口后，当 EXT-X-CUE 标记引用的媒体时间滚出滑动呈现窗口时，将从片段播放列表中删除该标记。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH 清单修饰 (MPD)

[MPEGDASH] 提供三种方式来发出事件信号：

1.  MPD EventStream 中发出信号的事件
2.  使用事件消息框 ('emsg') 在带内发出信号的事件
3.  两者的组合

MPD EventStream 中发出信号的事件可用于 VOD 流式处理，因为下载 MPD 后，客户端即可有权访问所有事件。 这也适用于 SSAI 信号，其中的下游 SSAI 供应商需要从多时段 MPD 清单分析信号，并动态插入广告内容。  带内 ('emsg') 解决方案可用于实时流式处理，其中的客户端无需再次下载 MPD，或者客户端与源之间不会发生任何 SSAI 清单操作。 

对于 DASH，Azure 媒体服务的默认行为是使用事件消息框 ('emsg') 在 MPD EventStream 中和带内发送信号。

通过 [RTMP] 或 [MS-SSTR-Ingest] 引入的提示消息将使用带内“emsg”框和/或 MPD EventStreams 映射为 DASH 事件。 

适用于 DASH 的带内 SCTE-35 信号遵循 [SCTE-214-3] 以及 [DASH-IF-IOP] 部分 13.12.2（“SCTE35 事件”）中的定义和要求。 

对于带内 [SCTE-35] 负载，事件消息框 ('emsg')使用 schemeId = "urn:SCTE:scte35:2013:bin"。 对于 MPD 清单修饰，EventStream schemeId 使用 "urn:scte:scte35:2014:xml+bin"。  此格式是事件的 XML 表示形式，其中包括引入时抵达的完整 SCTE-35 消息的二进制 base64 编码输出。 

[SCTE-214-1] 部分 6.7.4 (MPD) 和 [SCTE-214-3] 部分 7.3.2（SCTE 35 提示消息的负载）提供了 DASH 中 [SCTE-35] 提示消息的负载的规范引用定义。

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream 信号

事件的清单 (MPD) 修饰将使用 EventStream 元素在 MPD 中发送信号，该元素将显示在 Period 元素中。 使用的 schemeId 为 "urn:scte:scte35:2014:xml+bin"。

> [!NOTE]
> 为简明起见，[SCTE-35] 允许使用 Signal.Binary 元素（而不是 Signal.SpliceInfoSection 元素）中的 base64 编码部分来代替完整分析的提示消息的负载。
> Azure 媒体服务使用此 'xml+bin' 方法在 MPD 清单中发出信号。
> 这也是 [DASH-IF-IOP] 中建议使用的方法 - 请参阅标题为 [DASH IF IOP 的“广告插入事件流”指南](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)的部分
> 

EventStream 元素具有以下属性：

| **属性名称** | **类型**                | **必需？** | **说明**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | 必须      | 标识消息的方案。 方案将设为“Live Server Manifest”块中的方案属性的值。 该值**应该**是用于标识消息方案的 URN 或 URL；根据 [SCTE-214-1] 部分 6.7.4 (MPD)，支持的输出 schemeId 应该是 "urn:scte:scte35:2014:xml+bin"，因为出于 MPD 的简洁性，该服务目前仅支持 "xml+bin"。  |
| value              | string                  | 可选      | 方案所有者用于自定义消息的语义的附加字符串值。 为便于区分具有相同方案的多个事件流，值**必须**设为 事件流的名称（[MS-SSTR-Ingest] 的 trackName，或 [RTMP] 引入的 AMF 消息）。 |
| 时间刻度          | 32 位无符号整数 | 必须      | 时间刻度，以每秒为刻度单位。                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 使用 EventStream 发送 SCTE-35 的 MPEG DASH 清单 (MPD) 信号的示例

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> 请注意，presentationTime 是 [SCTE-35] 事件的呈现时间，经转换后相对于时段开始时间，而不是消息的抵达时间。
> [MPEGDASH] 将 Event@presentationTime 定义为“指定相对于时段开始时间的事件的呈现时间”。
> 呈现时间的值（以秒为单位）是此属性的值与 EventStream@timescale 属性的值相除的结果。
> 如果不存在，则呈现时间值为 0。


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 MPEG DASH 带内事件消息框信号

带内事件流要求 MPD 具有适应集级别的 InbandEventStream 元素。  此元素具有一个必需的 schemeIdUri 属性和可选的时间刻度属性，该属性还显示在事件消息块（“emsg”）中。  **不应**存在具有未在 MPD 中定义的方案标识符的事件消息框。

对于带内 [SCTE-35] 负载，信号**必须**使用 schemeId = "urn:scte:scte35:2013:bin"。
[SCTE-214-3] 部分 7.3.2（SCTE 35 提示消息的负载）中定义了 [SCTE-35] 带内消息的规范定义。

以下详细信息概述了客户端预期在 'emsg' 中使用的符合 [SCTE-214-3] 的特定值：

| **字段名称**          | **字段类型**          | **必需？** | **说明**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | 必须      | 标识消息的方案。 方案将设为“Live Server Manifest”块中的方案属性的值。 该值**必须**是用于标识消息方案的 URN。 对于 [SCTE-35] 消息，此值**必须**是符合 [SCTE-214-3] 的 "urn:scte:scte35:2013:bin" |
| Value                   | string                  | 必须      | 方案所有者用于自定义消息的语义的附加字符串值。 为便于区分具有相同方案的多个事件流，值将设为 事件流的名称（平滑引入的 trackName 或 RTMP 引入的 AMF 消息）。                                                                  |
| 时间刻度               | 32 位无符号整数 | 必须      | “emsg”块中的时间和持续时间字段的时间刻度（以“滴答数/秒”为单位）。                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 位无符号整数 | 必须      | 此片段中的事件呈现时间的媒体呈现时间增量和最早的呈现时间。 呈现时间和持续时间**应该**与类型 1 或 类型 2 的流访问点 (SAP) 保持一致，如 [ISO-14496-12] 附录 I 中所述。                                                                                            |
| event_duration          | 32 位无符号整数 | 必须      | 事件的持续时间或 0xFFFFFFFF，以指示未知的持续时间。                                                                                                                                                                                                                                                                                          |
| ID                      | 32 位无符号整数 | 必须      | 标识消息的此实例。 具有等效语义的消息应具有相同的值。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。                                                                                                                                                    |
| Message_data            | Byte Array              | 必须      | 事件消息。 对于 [SCTE-35] 消息，消息数据是符合 [SCTE-214-3] 的二进制 binary splice_info_section() |

### <a name="334-dash-message-handling"></a>3.3.4 DASH 消息处理

对于视频和音频轨迹，事件都是“emsg”块内的带内信号。  所有片段请求都会发出信号，因为 presentation_time_delta 为 15 秒或更少。 

启用滑动呈现窗口后，当事件消息的时间和持续时间之和小于清单中的媒体数据的时间时，将从 MPD 中删除事件消息。  换而言之，当事件消息引用的媒体时间滚出滑动呈现窗口时，将从清单中删除事件消息。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4.面向编码器供应商的 SCTE-35 引入实现指南

以下指导原则针对编码器供应商在实施此规范时可能遇到的常见问题。  以下指导原则是根据真实的合作伙伴反馈收集的，旨在更轻松地为其他人实施此规范。 

[SCTE-35] 消息针对 [MS-SSTR-Ingest] 使用方案 **“urn:scte:scte35:2013:bin”** 以二进制格式引入，并针对 [RTMP] 引入使用类型 **“scte35”** 。 为便于转换基于 MPEG-2 传输流呈现时间戳 (PTS) 的 [SCTE-35] 计时，事件呈现时间（对于平滑引入为 fragment_absolute_time 字段，对于 RTMP 引入为时间字段）在 PTS (pts_time + pts_adjustment of the splice_time()) 和媒体时间线之间提供了一种映射。 映射是必需的，因为 33 位 PTS 值每隔大约 26.5 小时将汇总一次。

平滑流式处理引入 [MS-SSTR-Ingest] 要求媒体数据框 (‘mdat’) **必须**包含 [SCTE-35] 中所定义的 **splice_info_section()** 。 

对于 RTMP 引入，AMF 消息的提示属性应设置为 [SCTE-35] 中定义的 base64 编码**splice_info_section()** 。  

如果消息采用上述格式，它们将发送到上面定义的 HLS、Smooth 和 DASH 客户端。  

使用 Azure 媒体服务平台测试实现时，请先使用“直通”LiveEvent 开始测试，然后继续对编码 LiveEvent 进行测试。

---

## <a name="change-history"></a>更改历史记录

| Date     | 更改                                                                            |
|----------|------------------------------------------------------------------------------------|
| 07/2/19  | 修订了 SCTE35 的 RTMP 引入支持，添加了 Elemental Live 的 RTMP“onCuePoint” | 
| 08/22/19 | 已做出更新，将 OnUserDataEvent 添加到自定义元数据的 RTMP                         |

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

