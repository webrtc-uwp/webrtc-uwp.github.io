
# WebRTC SDK for WinUWP

The WebRTC folder contains a sub folder named SDK:  
[WebRTC SDK](https://webrtc.googlesource.com/src/+/master/sdk/)  

This folder is used to hold native APIs for Android and iOS for native mobile WebRTC based application to run outside the browser context. These APIs wrap the WebRTC APIs in APIs accessible to Java and Objective-C and include helper routines for device enumeration / selection.

For WinUWP, a similar folder exists:  
[WebRTC SDK for WinUWP](https://github.com/webrtc-uwp/webrtc-apis/)  

This link contains two sub folders:

1. "idl" - Input files needed to generate the platform specific wrappers for WinUWP (and possibly other languages)
2. "windows" - The glue code to tie generated wrappers to the native WebRTC C++ code.

This folder is designed to be placed at the same folder level as the Android and iOS equivalents. However, unlike iOS and Android, the APIs for WinUWP wrappers are generated from IDL (Interface Definition Language) files. The sub-folder named "idl" contains these definitions. The "windows" sub-folder contains the glue code needed to tie the generated IDL outputs to the WebRTC native C++ code. The glue code does not directly contain the APIs used for end user access as those APIs are entirely generated specific to their IDL definitions. Please see [WebRTC UWP and IDL](idl.md).

The glue code in the "windows" sub-folder also includes code needed for the capturing and rendering of video specific to the WinUWP platform. This allows the code specific to the WinUWP platform to exist outside of the WebRTC cross platform C++ engine without needing to modify the native WebRTC code internals.

Currently, the SDK folder is partially built using additional GN files related to IDL and the final DLL/winmd code to expose WebRTC UWP as a component is built using a Visual Studio project. While the WinUWP code written using the C++ WinRT library is entirely compiler friendly using C++17 and clang, in practice at the moment only MSVC toolchains are officially supported. In the future an entirely GN / clang based process is possible but the effort level to move to that process has not been entirely scoped or tested to ensure it works properly.