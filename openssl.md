
# Compile OpenSSL for Windows

This receipt compiles the latest 32 bit OpenSSL as a static library.

Since people are unlikely to update the openssl dll's each time the openssl
project patches some really horryfying bugs, it's simpler to release our own
project when this happends. Then we also don't need to copy the openssl
dll's around, risking all kinds of interesting conflicts between installed
software. (It's especially convenient to use static libraries during development
to avoid distractions with DLL HELL).

The following things needs to be installed:
- Microsoft Visual Studio (I use the free community edition)
- Perl (I use ActivePerl)
- Nasm (I install it to C:\devel)

Open a command shell with the Microsoft Visual Studio environment variables set.
```
cd C:\devel
git clone https://github.com/openssl/openssl.git
cd openssl
PATH=%PATH%;C:\DEVEL\NASM
perl Configure  VC-WIN32  --prefix=c:\devel\openssl\lib-386
nmake -f ms\nt.mak
nmake -f ms\nt.mak install
```
