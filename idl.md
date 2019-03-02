
# WebRTC UWP and IDL

## Overview

For the WebRTC UWP project, we've decided to use [IDL](https://en.wikipedia.org/wiki/IDL_specification_language) (Interface Definition Language) to auto-generate the wrapper code we need to expose the internal C++ APIs in other language bindings we wish to expose to developers externally. IDL defines a set of objects, structures, lists, maps, sets, dictionaries, properties, exceptions, events, and asynchronous promises in a "header" only style simple language.

## Rationale

Specifically for the WebRTC project, our target was C#, JS, and .NET. To do this on Universal Windows requires wrapping each API in C++ code. Originally our APIs were manually written in C++/CX (a Microsoft only C++ extension language), but maintaining the code base against a moving API set (i.e. WebRTC internal code) while not messing up the rules to convert to and from binded objects proved challenging.

The manual bindings were often subject to copy and paste errors and required heavy lifting every time a new API would be introduced or changed.

## Move to IDL for C++/WinRT

With the introduction of [C++/WinRT](https://github.com/Microsoft/cppwinrt), all the old C++/CX code would need to be discarded and replaced with a new implementation. Unlike C++/CX, C++/WinRT requires only a modern, standards-compliant C++17 compiler and uses no special language extensions. The C++ tooling provided by Microsoft takes [Microsoft IDL](https://docs.microsoft.com/en-us/windows/desktop/midl/midl-start-page) and generates a binary `.winmd` file (Windows metadata) describing the entire API surface available. Another tool takes the `.winmd` file and generates C++ stubs matching the API description which are then filled out with an implementation.

### Why not use Microsoft IDL?

The WebRTC UWP project does not use Microsoft IDL directly, but instead uses "SW" IDL input files. These IDL files are consumed by a tool which generates Microsoft IDL as well as all the implementation needed for the C++/WinRT stub files and spits out glue code that needs to be implemented between the exposed API language (in this case C++/WinRT) and the native API implementation.

This may seem redundant but there are key advantages.

### Advantages of "SW" IDL

SW IDL isn't restricted to being able to only generate and link with Microsoft based APIs. The current generator supports C++/WinRT and legacy C++/CX generated wrappers. Other language bindings have been implemented too such as C, C#.NET, and Python. More language support is under way.

### Why not SWIG/Corba/DCOM?

These IDL languages have served their era well but they are getting old now. SW modernizes both the IDL and their outputs supporting common modern and common language paradigms such as objects, interfaces, multiple inheritance, generics, events, promises and coroutines.

Other language features are intentionally omitted, like pointers, handles and similar low-level constructs. This allows for the IDL to be both portable and modern without having to introduce risky and unstable API bindings.

SW IDL is very similar to what would be defined in WebIDL except it's not web specific. Nothing stops the SW IDL from generating SWIG IDL, Corba, or other IDL-based language bindings.

## Current implementation status

The current generated IDL output is dependent upon a library called `zsLib`. This library handles basic async capabilities (events and queues) needed to make IDL work (even for languages that are single threaded). A new implementation is underway to replace this library with another called SW library which will be an MIT-licensed library with official corporate sponsorship (TBA). As part of this change, SW language binding support will expand and support both authoring and consuming in multiple languages as well as efforts for remote API calling.

## Dependency on SW

SW library is a cross platform API set used for convenience in the generated C++ code for the bindings from C++ to other languages. This library is necessary to control asynchronous APIs and events and ensure every bound language is handled properly. As SW is a static library, unused routines will be linked out.

## Implementation Steps Overview

1. Build the SW IDL tool
2. Write IDL definitions
3. From IDL files+tool, generate authoring "stubs" ONCE only
4. Implement authoring stubs and check into git
5. Generate language bindings as needed for consumers of the authored APIs

## Example IDL files

https://github.com/webrtc-uwp/webrtc-apis/tree/master/idl  

## Example Glue implementation

https://github.com/webrtc-uwp/webrtc-apis/tree/master/windows/wrapper  
