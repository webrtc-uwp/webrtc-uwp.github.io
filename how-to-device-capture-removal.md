# Creating a PeerConnection without capture devices

Scenarios involving peers without audio or video capture devices should follow these steps.  This includes instances running on a server or an Xbox wihout Kinect.

1. Removing audio capture: Audio capture and render devices can be selectively removed by setting parameters on `WebRtcFactoryConfiguration`.  See the following example which could be used in `Conductor.cs` in the `PeerCC` sample.
   ```
    var factoryConfig = new WebRtcFactoryConfiguration();
    factoryConfig.AudioCapturingEnabled = false;
    factoryConfig.AudioRenderingEnabled = false;
    _factory = new WebRtcFactory(factoryConfig);

    var config = new RTCConfiguration
    {
        Factory = _factory
    };
    var peerConnection = new RTCPeerConnection(config);
   ```
2. Removing video capture: It is sufficient not to create a local video track. No need to open video capturer, as well.