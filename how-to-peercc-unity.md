# PeerCC Sample - Unity video rendering on HoloLens

The [PeerCC](https://www.github.com/webrtc-uwp/PeerCC) sample application includes both Unity and XAML front ends.  This document describes the process of building the sample.

> Note: The Unity sample requires a full clone of the WebRTC UWP SDK due to additional dependencies on `MediaEngine` and `WebRtcSchema`.  The [PeerCC-Sample](https://github.com/webrtc-uwp/PeerCC-sample) repo does not include Unity support, though they both use the same NuGet package.  The instructions below assume you have followed the instructions in the [webrtc-uwp-sdk](https://github.com/webrtc-uwp/webrtc-uwp-sdk) repo.

## Unity build requirements

* Unity version 2018.3.6f1 with IL2CPP Scripting Backend

## Running Unity Peer Connection Client application on HoloLens device 

1. Open `webrtc\windows\solutions\WebRtcUnity.sln` solution, select x86 platform and build `PeerConnectionClientUnityCore` project. The build procedure produces WebRTC libraries, WebRTC for UWP wrapper component and deploys WebRTC and Peer Connection Client Core libraries to the Unity project space.
2. Open the Unity project `common\windows\samples\PeerCC\ClientUnity` in Unity Editor
3. Go to 'File' -> 'Build settings...', select 'Universal Windows Platform', click on the 'Build' button and choose an export folder
4. Add the following XML block to the generated manifest file `PeerCCUnity\Package.appxmanifest`:
```
  <Extensions>
    <Extension Category="windows.activatableClass.inProcessServer">
      <InProcessServer>
        <Path>WebRtcScheme.dll</Path>
        <ActivatableClass ActivatableClassId="WebRtcScheme.SchemeHandler" ThreadingModel="both" />
      </InProcessServer>
    </Extension>
  </Extensions>
```
5. Open `PeerCCUnity.sln` from the export folder, build the `PeerCCUnity` project and run the application on the device