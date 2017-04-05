# Building libbost for Visual Studio 2017

Visual Studio 2017 is not supported out of the box by the current stable boost version (1.63),
so I have jumped to 1.64 beta.

Unpack to C:\devel\boost_1_64_0

Open a command prompt for Visual Studio 2017.

This builds boost for 64 bit architecture
```sh
bootstrap
b2 toolset=msvc-14 address-model=64 --build-type=complete stage
```
