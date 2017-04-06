# Compile the latest OpenSSL with Visual Studio

The following things needs to be installed:
- Microsoft Visual Studio (I use the free community edition)
- Perl (I use ActivePerl)
- Nasm (I install it to C:\devel)

Open a command shell with the Microsoft Visual Studio environment variables set.

## Build openssl for 64 bit architecture

```sh
git clone https://github.com/openssl/openssl.git
cd openssl
PATH=%PATH%;C:\DEVEL\NASM
perl Configure VC-WIN64A
nmake
nmake install
```
