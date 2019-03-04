# Access to raw video frame data

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
           configuration.AudioCaptureFrameProcessingQueue = Org.WebRtc.EventQueue.GetOrCreateThreadQueueByName("AudioCaptureProcessingQueue");
           configuration.AudioRenderFrameProcessingQueue = Org.WebRtc.EventQueue.GetOrCreateThreadQueueByName("AudioRenderProcessingQueue");
           configuration.VideoFrameProcessingQueue = Org.WebRtc.EventQueue.GetOrCreateThreadQueueByName("VideoFrameProcessingQueue");
           Org.WebRtc.WebRtcLib.Setup(configuration);
```