## This project has been deprecated

We are currently focusing our efforts on getting out of the fork business. This effort is happening in the [WinRTC GitHub repo](https://github.com/microsoft/winrtc). Keeping WebRTC-UWP fork doesn't allow us to move fast enough. We are contributing back the changes needed to build WebRTC.org code base for UWP. Here are some of the changes we're contributing back:

- https://webrtc-review.googlesource.com/c/src/+/167021
- https://boringssl-review.googlesource.com/c/boringssl/+/39584
- https://chromium-review.googlesource.com/c/chromium/src/+/1962509
- [abseil/abseil-cpp#594](https://github.com/abseil/abseil-cpp/pull/594)
- [abseil/abseil-cpp#596](https://github.com/abseil/abseil-cpp/pull/596)

Besides the new video capturing module that is being reviewed, we're also creating a new audio capturing module. There are more changes in the pipeline to be contributed back and more changes required for finishing the port. After having WebRTC.org code base compatible with UWP, we're going to work on a WinRT abstraction layer allowing easy consumption of WebRTC capabilities by WinRT projections.

That said, keep in mind we are contributing back the changes and we have no control over when/if the changes will be accepted by their teams.

# WebRTC for the Universal Windows Platform

This is the central point for documentaion for WebRTC on UWP.  Here you can find pointers to samples, links to the complete SDK and development how-tos.

Some repositories which can be cloned independently have `readme.md` files that list basic requirements.  In general those repositories link back to this site for more detailed information.

Please submit any documentation issues or requests to this repository.

## Current Status

The latest working branch `releases/m71`.  This branch currently includes support for Unity & HoloLens with known issues (see below).  Please direct PRs to this branch.

## Supported Platforms

The following platforms are supported in the current release:

- **UWP**: X86, x64 & ARM (32 bit) platforms are supported.  Builds can be completed either through Visual Studio or via the `run.py` script.  See the documentaion in the `webrtc-uwp-sdk` repo for additional information.
- **Unity via WinRT**: Unity support via the standard WinRT API surface is available and demonstrated in both `XAML` & `Unity` sample applications.  See the [Unity](how-to-peercc-unity.md) docs for more information.
- **Unity `SimplePeerConnnection` plugin**: Google provides Unity samples which leverage a plugin called `SimplePeerConnection` on other platforms.  An example of this plugin is available in the `cse/m71-ks` branch of the `webrtc-uwp-sdk` repo.
- **.NET Standard 2.0**: A "headless" version of the WebRTC APIs is available as a .NET Standard 2.0 dll.  Build the `WrapperC` project of the `WebRtc.Win32.sln` solution to produce the output file `libOrg.WebRtc.WrapperC.dll` in order to use this functionality.
  > **Note**: Currently only raw audio and video received from a remote peer are available in .NET Standard 2.0.  Additional work is underway to allow audio & video injection from a "headless" peer, and a sample application to demonstrate rendering video using this Dll is planned for future development.

## Branching & WebRTC.org Syncs

WebRTC UWP is periodically merged with the WebRTC.org source to maintain currency.  The branches named `releases/m71` are merged with the Chrome milestone of the same number.  The features and API for these releases are equivalent to those in the same version of Chrome.  For more information on the features in any given branch see the [WebRTC release notes](https://groups.google.com/forum/#!searchin/discuss-webrtc/webrtc$20release$20notes%7Csort:date).

## Known Issues

- ARM64 is not yet supported.  Please log an issue if this is important to you.
