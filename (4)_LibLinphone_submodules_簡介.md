# LibLinphone 的 submodules 簡介


LibLinphone 是一個 high-level API library，它提供一個介面而避免直接使用 submodules。本文件是介紹各 submodules，以方便了解 LibLinphone 的運作。


<a name="Introduction_of_sublibraries">Linphone 內各 sub-libraries 的分析</a>
---------------------------------

linphone library 主要的架構可以參考 [linphone.org 的介紹](http://www.linphone.org/technical-corner/liblinphone.html)。

### 相依性

在 `liblinphone-sdk/apple-darwin/lib` 底下，包括所有跟 linphone 有關的 static libraries。其中與 liblinphone 有關的約有 17 個，於下方列出 （用 parentheses 的表示為重複出現的 library；用 square brackets 的表示為 iOS SDK 已經有該 library，不需再額外加入）：

* liblinphone: linphone 提供 high-level 的 API (Facade)，避免直接使用 low-level library。
	* libortp: oRTP。實作出 RTP（Real-time Transport Protocol），多媒體數據流的傳輸協議。
		* libsrtp: SRTP (Secure Real-time Transport
Protocol)，提供 VoIP 安全的環境。
		* libbzrtp: [ZRTP](http://www.zrtp.org/zrtp-protocol)，a cryptographic key-agreement protocol。
			* libpolarssl: 實作 SSL and TLS protocols
			* [libxml2]: XML C parser
	* libmediastreamer_base: voice/video streaming
		* (libortp) 
	* libmediastreamer_voip: voice/video streaming
		* (libmediastreamer_base)
		* (libortp)
		* (libsrtp)
		* libspeex: audio compression format (BSD license)
		* libspeexdsp: 回音抑制、抗抖動等的演算法
		* libopus: opus codec (lossy audio coding format)
		* libgsm: libgsm codex (GSM Full Rate audio, speech coding)
		* libvpx: 實踐 VP8，為影片壓縮格式，達到 video streaming 
		* libswscale: 為 FFmpeg 底下的一個元件，為對於影像作縮放的函式庫 (LGPLv2.1)
		* libavcodec: 為 FFmpeg 底下的一個元件，包含了全部FFmpeg音訊／視訊編解碼函式庫
			* libavutil: 為 FFmpeg 底下的一個元件，為一些工具函式庫
		* [AudioToolbox]: 
	* libbellesip: SIP User Agent，處理 Signalling stack
		* libantlr3c: [ANTLR v3 C Runtime](https://github.com/esteve/libantlr3c): ANother Tool for Language Recognition.
		* (libpolarssl)
		* [libresolv]: 
	* [libxml2]
	* [libsqlite3] 




### 其他的 libraries

除了 libcunit 和 liblinphonetester 兩者為 UnitTest 之外，其他

#### Unit Test 
* libcunit: CUnit (unit tests in C)
* liblinphonetester: Linphone Test

#### For Mediastreamer Plugins

* libilbc: iLBC (Internet Low Bitrate Codec, audio codecs)
* libopencore-amrnb: AMR-NB (Adaptive Multi-Rate Narrowband)
* libopencore-amrwb: AMR-WB (Adaptive Multi-Rate Wideband)
* libopenh264: OpenH264 (encoding video streams into the H.264/MPEG-4 AVC format)
* libSKP_SILK_SDK: SILK codec (audio compression format and audio codec)
* libswresample: lswr (handles audio resampling, sample format conversion and mixing)
* libx264: video codec

#### Tunnel

* libtunnel: 通過防火牆


### Mediastreamer Plugins

* libmsamr: AMR (Adaptive Multi-Rate, audio codec, an audio compression format optimized for speech coding)
	* 需要 libopencore-amrnb 和 libopencore-amrwb
* libmsbcg729: G729A codec (audio data compression)
* libmsilbc: iLBC (internet Low Bitrate Codec)
	* 需要 libilbc 
* libmsopenh264: OpenH264 (encoding video streams into the H.264/MPEG-4 AVC format)
* libmssilk: SILK codec (audio compression format and audio codec)
	* 需要 libSKP_SILK_SDK
* libmswebrtc: WebRTC
* libmsx264: x264 (encoding video streams into the H.264/MPEG-4 AVC format)
	* 需要 libx264

