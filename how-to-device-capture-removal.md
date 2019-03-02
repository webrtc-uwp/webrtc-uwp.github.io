# Creating a PeerConnection without capture devices

Scenarios involving peers without audio or video capture devices should follow these steps.  This includes instances running on a server.

1. Removing audio capture: WebRtc initialization should send `false` for the first parameter to indicate that audio recording devices won't be used: `Org.WebRtc.WebRtcLib.Setup(queue, false, true)`.
2. Removing video capture: It is sufficient not to create a local video track. No need to open video capturer, as well.