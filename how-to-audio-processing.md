# Audio pre and post processing

The WebRTC M71 release enabled audio pre and post processing as described [here](https://bugs.chromium.org/p/webrtc/issues/detail?id=8665).  The same classes are available to managed code in WebRTC UWP via the WebRtcFactory class.  

> Note that pre & post processing is not available per-stream.  The filters can be specified per-WebRtcFactory.  WebRtcFactory can be instantiated multiple times per application if separate filters are needed for separate streams or PeerConnections.

These methods are necessarily blocking, as they are in Chrome.  Handlers with long execution times could cause issues.  By default, the handlers will run on the UI thread and could interfere with interactivity.  To prevent this, provide thread queues for the audio callbacks during intialization as follows:

```C#
public void Initialize(CoreDispatcher uiDispatcher)
{
    _uiDispatcher = uiDispatcher;

    var queue = Org.WebRtc.EventQueueMaker.Bind(uiDispatcher);
    var configuration = new Org.WebRtc.WebRtcLibConfiguration();

    configuration.Queue = queue;
    configuration.AudioCaptureFrameProcessingQueue = Org.WebRtc.EventQueue.GetOrCreateThreadQueueByName("AudioCaptureProcessingQueue");
    configuration.AudioRenderFrameProcessingQueue = Org.WebRtc.EventQueue.GetOrCreateThreadQueueByName("AudioRenderProcessingQueue");
    Org.WebRtc.WebRtcLib.Setup(configuration);
}
```

The below sample code from `Conductor.cs` in the `PeerCC` sample demonstrates how to register for these events.

```C#
var factoryConfig = new WebRtcFactoryConfiguration();
_factory = new WebRtcFactory(factoryConfig);

_factory.OnAudioPostCaptureInitialize += Factory_HandleAudioPostCaptureInit;
_factory.OnAudioPostCaptureRuntimeSetting += Factory_HandleAudioPostCaptureRuntimeSetting;
_factory.OnAudioPostCapture += Factory_HandleAudioPostCaptureBuffer;

_factory.OnAudioPreRenderInitialize += Factory_HandleAudioPreRenderInit;
_factory.OnAudioPreRenderRuntimeSetting += Factory_HandleAudioPreRenderRuntimeSetting;
_factory.OnAudioPreRender += Factory_HandleAudioPreRenderBuffer;
```
The following sample code from the same file shows how to generate a SIN wave as input to an audio stream.

```C#
private void DisplayAudioBufferEvent(
            ref int totalSamples,
            String type,
            Org.WebRtc.IAudioBufferEvent evt)
{
    var buffer = evt.Buffer;
    var data = buffer.Channel(0);
    var array = data.Data.ToArray();

    double scale = Math.Pow(2, 10);

    for (int index = 0; index < array.Count(); ++index, ++totalSamples)
    {
        array[index] = (short)(Math.Sin(totalSamples) * scale);
    }

    data.Data = array;
}
```