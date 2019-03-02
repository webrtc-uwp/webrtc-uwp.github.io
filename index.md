# WebRTC for the Universal Windows Platform

This is the central point for documentaion for WebRTC on UWP.  Here you can find pointers to samples, links to the complete SDK and development how-tos.

Some repositories which can be cloned independently have `readme.md` files that list basic requirements.  In general those repositories link back to this site for more detailed information.

Please submit any documentation issues or requests to this repository.

## Current Status

The latest working branch `releases/m71`.  This branch currently includes support for Unity & HoloLens with known issues (see below).  Please direct PRs to this branch.

## Branching & WebRTC.org Syncs

WebRTC UWP is periodically merged with the WebRTC.org source to maintain currency.  The branches named releases/m71 are merged with the Chrome milestone of the same number.  The features and API for these releases are equivalent to those in the same version of Chrome.  For more information on the features in any given branch see the [WebRTC release notes](https://groups.google.com/forum/#!searchin/discuss-webrtc/webrtc$20release$20notes%7Csort:date).

## Known Issues

- H.264 video freezes after a few frames.  Fix expected by March 15.
- ARM is currently unsupported (but under development).  For ARM support, use M54 or earlier releases.
- Audio device selection was removed from the Google WebRTC APIs, and is therefore no longer supported here.  Playback occurs on the default device as specified by Windows.