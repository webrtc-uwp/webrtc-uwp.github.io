# How to enable & analyze WebRTC traces

WebRTC traces are included in the UWP library and are equivalent to what is present in Chrome.  Currently they are not wired to the managed API surface.  The notes below refer to enabling traces in native code.  This will be enabled for managed clients in a future release.

Google's sample to enable traces can be found [here](https://chromium.googlesource.com/external/webrtc/+/refs/heads/master/sdk/android/src/jni/pc/peer_connection_factory.cc). It is an Android component where the traces are saved to the file.

Call `rtc::tracing::SetupInternalTracer()` and after that `rtc::tracing::StartInternalCapture()` with a file name as a parameter. After tracing session is ended, you can visualize the results in Chrome browser using `chrome://tracing` application.