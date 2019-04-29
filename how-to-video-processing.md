# Access to raw video frame data

Raw video frame data is available via the `VideoCapturer.OnVideoFrame` event.  The following code from the `PeerCC` sample application demonstrates its use.

> Note: The video processing handlers are non-blocking by default.  However, to prevent slowdown in the UI it is recommended to specify `Org.WebRtc.WebRtcLibConfiguration.VideoFrameProcessingQueue` so that the handler runs on a separate thread as is done in the sample below.

```C#
Process_VideoFrameBufferEvent
#if ENABLE_VIDEO_PROCESSING
           ((VideoCapturer)videoCapturer).OnVideoFrame += (IVideoFrameBufferEvent evt) =>
           {
               Process_VideoFrameBufferEvent(evt);
           };
#endif //ENABLE_VIDEO_PROCESSING
var queue = Org.WebRtc.EventQueueMaker.Bind(uiDispatcher);
           var configuration = new Org.WebRtc.WebRtcLibConfiguration();
           configuration.Queue = queue;
           configuration.VideoFrameProcessingQueue = Org.WebRtc.EventQueue.GetOrCreateThreadQueueByName("VideoFrameProcessingQueue");
           Org.WebRtc.WebRtcLib.Setup(configuration);
```