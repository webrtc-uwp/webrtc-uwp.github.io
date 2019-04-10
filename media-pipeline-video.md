# WebRTC Media Pipeline - Video

The WebRTC video capture, encode & decode pipelines differ depending on whether it is in use for a native SDK or inside Chrome.  This document outlines those differences for context, then describes the native UWP & .NET implementations developed by Microsoft.

## WebRTC in Chrome

Media handling in WebRTC when running in Chrome is handled by external (to WebRTC) components. You can find the references to Chromium media processing resources here:
https://github.com/chromium/chromium/tree/master/content/renderer/media/webrtc
https://github.com/chromium/chromium/tree/master/media/gpu/windows

- **H.264 Decoder:** Chrome uses `FFMPEG` to decode the stream which will use Direct3D H.264 hardware decoding if available.  On Windows N SKUs without the Media Pack where no system H.264 codec is present, it will fall back to `OpenH264` software decoding.
- **H.264 Encoder:** Media Foundation Transform (MFT) uses hardware where available.  `NV12` output is converted to `I420` using libyuv (CPU).
- **Video Capturer:**  [MF based](https://bugs.chromium.org/p/chromium/issues/detail?id=735576) - MFCreateDeviceSource() with DirectShow used for fallback.  WRL is used to keep the references to COM objects which is part of WinRT API.
  - [Testing](https://chromium-review.googlesource.com/c/chromium/src/+/1000853/7/media/capture/video/win/video_capture_device_factory_win.cc): DirectShow virtual cameras are added to the array of MF device as MF does not have a virtual camera.

> Note: Other Chrome platforms are not considered here.

## Google Native WebRTC SDKs

### Win32

- **H.264 Decoder:** `FFMPEG` is used, similar to the Chrome implementation.
- **H.264 Encoder:** `OpenH264` - SW codec - temporal and spatial SVC, simulcast
- **Video Capturer:** `DirectShow` used to capture video.

### UWP & .NET Standard 2.0

- [H.264 Decoder](https://github.com/webrtc-uwp/webrtc-windows/tree/releases/m71/third_party/winuwp_h264/H264Decoder): The UWP & .NET native components utilize an MFT-backed (Media Foundation Transform) H.264 forward decoder implementation which is internal to WebRTC.  This uses the same codepath as VPX decoders.  NV12 output is converted to I420 using `libyuv` (CPU).
  - [Unit Tests](https://github.com/webrtc-uwp/webrtc/pull/10#pullrequestreview-220220989): H.264 decoder in the UWP stack has gtest coverage..
  - See the TODO section at the top of H264Decoder.cc for a list of pending tasks which will be addressed in future PRs.
- [H.264 Encoder](https://github.com/webrtc-uwp/webrtc-windows/tree/releases/m71/third_party/winuwp_h264/H264Encoder): Implented as a custom `IMFMediaSink`.  NV12 output is converted to I420 using `libyuv` (CPU). Source [here](https://github.com/webrtc-uwp/webrtc-windows/tree/releases/m71/third_party/winuwp_h264/H264Encoder).
- [Video Capturer](https://github.com/webrtc-uwp/webrtc-apis/blob/releases/m71/windows/wrapper/impl_webrtc_VideoCapturer.h): Custom `IMFMediaSink` - `NV12` output is converted to `I420` using libyuv (CPU).  This is implemented as a C++/WinRT external component.

#### Raw frame access

Raw frame access is available for both H.264 and VPX codecs in the UWP & .NET stack.  This is used for scenarios including remote video composition and ML.

## UWP & .NET Standard differences & potential issues

### Encoder & Decoder

- When running on Windows "N" editions which lack the H.264 codec, Chrome falls back to OpenH264.  Compatibility between OpenH264 and the MF encoders/decoders should be tested to ensure Chrome hardware and software accelerated H264 implementations are compatible.

### Simulcast & SVC

- The UWP stack is current with M71 and does not yet include [Simulcast](https://webrtc-review.googlesource.com/c/src/+/64100) & [SVC](https://webrtc-review.googlesource.com/c/src/+/86140) for H.264.  This has been investigated and we plan to use multiple MF encode engines to do the work, no issues anticipated from a platform standpoint.  Still TBD if we can leverage MF optimizations for multiple encodings.