---
title: HLS (Http Live Streaming)
tags:
  - HLS
  - Streaming
date: 2018-08-29 22:35:34
---


## Overview

HLS는 Apple에서 만든 HTTP 기반의 스트리밍 프로토콜이다. 현재 기준 (2018.08) 최신 프로토콜 버전은 8이고, 관련 스펙은 [2nd Edition](https://tools.ietf.org/html/draft-pantos-hls-rfc8216bis-02#page-4)으로 계속 업데이트 되고 있다.



#### 구성 요소

![](https://docs-assets.developer.apple.com/published/88e87744a3/de18e941-81de-482f-843d-834a4dd3aa71.png)

이미지 출처: <https://developer.apple.com/documentation/http_live_streaming>



HLS는 `Playlist`라고 부르는 `.m3u8` 파일을 통해 서비스 되는데 그 과정은 다음과 같다.

* Input(Content)이 MPEG-TS로 인코딩되고
* 인코딩 된 파일을 고정 값(duration)을 가지는 .ts 파일들로 작게 나누고 @서버
* 그 파일들을 인덱싱해서 playlist 파일로 서비스하고
* URL로 .m3u8 및 .ts 파일을 요청 및 다운로드 받아 재생하고 @클라이언트

즉, 서버에서는 컨텐츠를 인코딩해서 자르고 클라이언트에서는 그 조각난 파일들을 받아 재생한다.



## Adaptive Streaming

HLS의 탄생 배경을 이해하기 위해 Adaptive Streaming에 대해 알고 있으면 좋다.

Adaptive Streaming이란 Multi-bitrate을 지원하는 스트리밍 방식을 말한다. 실시간으로 네트워크 상태에 따라 High&lt;-&gt;Low Bitrate switching을 하며 `끊김없는 재생`을 목표로 한다. 기존에는 전체 100을 한 번에 다운로드 받았다면, Adaptive에서는 전체 100을 10개의 조각(segment)으로 나누어 그 때 그 때 네트워크 상황에 맞추어 High-Mid-Low Bitrate stream을 선택하는 것이다. 이는 결국 `버퍼링을 최소화`하고 최적의 환경을 제공하기 위함이다.

HLS는 바로 이 Adaptive streaming의 한 종류이다. 대표적인 다른 프로토콜은 MPEG-DASH, Microsoft Smooth Streaming 등이 있다.



## Playlist

HLS는 Manifest로 `.m3u8` 확장자를 가지는 `playlist` 파일을 사용한다. (각 프로토콜 별로 Manifest 파일의 확장자가 다르다.) playlist에는 `Master Playlist` 그리고 `Media Playlist` 두 종류가 있다. Master Playlist가 Media Playlist를 품고 있는 구조이다. Master Playlist에는 Stream 전체에 대한 정보가 Media Playlist에는 Segment에 대한 정보가 있다고 보면 된다. 

Example을 보면 이해하기 좋을 것 같다. 공식 Draft에 있는 Example을 발췌하여 약간 수정했다.

**Master Playlist**

```
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=1280000,AVERAGE-BANDWIDTH=1000000
http://example.com/low.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=2560000,AVERAGE-BANDWIDTH=2000000
http://example.com/mid.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=7680000,AVERAGE-BANDWIDTH=6000000
http://example.com/hi.m3u8
```

**Media Playlist**

```
#EXTM3U
#EXT-X-TARGETDURATION:10
#EXT-X-VERSION:3
#EXTINF:9.009,
first.ts
#EXTINF:9.009,
second.ts
#EXTINF:3.003,
third.ts
#EXT-X-ENDLIST
```

예제에는 없지만 처음에 Master Playlist를 다운로드 받기 위한 URL이 있을 것이고, 그 파일의 내용이 위와 같다고 하자. `BANDWIDTH` 속성 값으로 나누어져 있는 URL이 Media Playlist를 다운로드 받기 위한 URL이 된다. - low.m3u8 / mid.m3u8 / hi.m3u8

Multi-bitrate으로 인코딩 된 컨텐츠를 제공할 뿐, 동시에 여러 개의 Bandwidth를 재생하는 것은 아니다. 따라서 재생 시점에는 현재 네트워크 상황에 맞는 하나의 Media Playlist가 선택되고, 해당 Media Playlist에 명시된 .ts 파일들을 순차 재생하게 된다.

예를 들어, Media Playlist로 <http://example.com/mid.m3u8> URL이 선택되었다면 처음 .ts 파일을 받기 위한 URL은 <http://example.com/mid/first.ts> 같은 형식이 되는 것이다. High-bitrate으로 Switching 되어 재생을 이어간다면, <http://example.com/hi/second.ts> 파일을 다운로드 받게 될 것이다.

즉, 각 Media Playlist에 있는 .ts 파일들은 동일한 컨텐츠를 담고 있는 것이다. `#EXTINF` Tag는 Duration 값을 나타내는데 아래 파일들 모두 9초 분량의 동일 컨텐츠인 셈이다. 물론 해상도는 다를 것이다.

* low/first.ts
* mid/first.ts
* hi/first.ts



---

다 아는 내용이라 금방일 줄 알았는데 괜찮은 글로 정리하려니 시간이 오래 걸린다. Tag는 굉장히 많아서 그 소개는 다음 포스팅으로 넘어가야 할 것 같다. 🧐
