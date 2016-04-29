# Documentation

This "project" is dedicated to documentimng certain things that are useful
to know in some of my projects.

# Compiling libraries under Windows

To save time, I use default settings for common libraries in my cmake files.
Here are some motes to myself about how to compile these libraries to their
(my) default locations

## OpenSSL

This receipt compiles the latest 32 bit OpenSSL as a static library.

Since people are unlikely to update the openssl dll's each time the openssl
project patches some really horryfying bugs, it's simpler to release our own
project when this happends. Then we also don't need to copy the openssl 
dll's around, risking all kinds of interesting conflicts between installed
software. (It's especially convenient to use static libraries during development
to avoid distractions with DLL HELL).

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
