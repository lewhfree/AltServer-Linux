# AltServer-Linux
AltServer for AltStore, but on-device

lewhfree version of this is built so that people using armv6/armv6hf (rpi zero / zero w) can build and run altserver-linux

## Usage

- Install IPA: `./AltServer -u [UDID] -a [AppleID account] -p [AppleID password] [ipaPath.ipa]`
- Running as AltServer Daemon: `./AltServer`
- I highly suggest running your own anisette server [using this](https://github.com/Dadoum/anisette-v3-server) or [this](https://docs.sidestore.io/docs/advanced/anisette/).
- Full usage (maybe outdated, refer to `./AltServer -h` for the newest):
```
Usage:  AltServer-Linux options [ ipa-file ]
  -h  --help             Display this usage information.
  -u  --udid UDID        Device's UDID, only needed when installing IPA.
  -a  --appleID AppleID  Apple ID to sign the ipa, only needed when installing IPA.
  -p  --password passwd  Password of Apple ID, only needed when installing IPA.
  -d  --debug            Print debug output, can be used several times to increase debug level.

The following environment var can be set for some special situation:
  - ALTSERVER_ANISETTE_SERVER: Set to custom anisette server URL
          if not set, the default one: https://armconverter.com/anisette/irGb3Quww8zrhgqnzmrx, is used
  - ALTSERVER_NO_SUBSCRIBE: (*unused*) Please enable this for usbmuxd server that do not correctly usbmuxd_listen interfaces
```

## Download

- Precompiled static binary can be downloaded in Release ( also have a look at pre-release ;) )
- Nightly version is available as Github Actions artifacts

## TODO / Special Features
- [ ] Fix my fork of darling-corecrypto to support make install
- [x] Track upstream (AltServer-Windows) develop branch (i.e. Beta version)
- [x] Support Offline Anisette Data Generation (i.e. without Sideloadly)
  - Finsihed, please run [alt_anisette_server](https://hub.docker.com/r/nyamisty/alt_anisette_server) & use `ALTSERVER_ANISETTE_SERVER` to specify custom server URL
- [x] Support Wi-Fi Refresh
  - [netmuxd](https://github.com/jkcoxson/netmuxd) now supports network devices (needs version > v0.1.1, be sure to check pre-release)
    - Download `netmuxd`, stop the original `usbmuxd`, and run `netmuxd` before running `AltServer-Linux`
    - ~If netmuxd does not work, please try using special env var `ALTSERVER_NO_SUBSCRIBE`. Enabling this would disable **auto-refresh when plugged-in** of USB devices~

----

## Advanced: Build Instruction
- Prerequisites / Before you Build
  ```
	1. Docker installed and set up
  		- Either install yourself or run
  			$ sudo curl -sSL https://get.docker.com | sh
  		- If you get an error google "How to install docker **YOUR OPERATING SYSTEM and VERSION**
  ```
- Build  
  ```
    1. Run alpine docker (change --platform to corresponding architecture you want): 
        docker run --platform=linux/arm/v6 --name altserver-builder-alpine -it alpine:3.15
  	-- Everything from now on unless specified is inside this Docker container.
    2. Install dependencies:
  	# cd
  	# apk add zsh git curl wget g++ clang boost-static ninja boost-dev cmake make sudo bash nano libressl-dev util-linux-dev zlib-dev zlib-static python3 py3-pip py3-cryptography
    3. Install corecrypto
  	-- Using lewhfree fix of the darling implementation of corecrypto because it plays better with my compiler and cmake (and it uses GNU GPLv3)
  	# git clone --recursive https://github.com/lewhfree/corecrypto
  	# cd corecrypto
  	# mkdir build
  	# cd build
  	# CC=clang
  	# CXX=clang++
  	# cmake -DENABLE_TESTS=ON ..
  	# make
  	--Dont do this command.
  	# cp libcorecrypto.so /usr/lib
    4. Install cpprestsdk
  	# git clone --recursive https://github.com/microsoft/cpprestsdk
	# cd cpprestsdk
  	# mkdir build
  	# cmake -DBUILD_SHARED_LIBS=OFF ..
  	--Skip this if you arent compiling for armv6/armhf or armv7
  	# find . -type f -exec grep -l " -Wcast-align" {} \; | xargs sed -i 's/ -Wcast-align//g'
  	# make
  	# make install
    5. Install libzip
        # git clone https://github.com/nih-at/libzip
  	# cd libzip
  	# mkdir build
  	# cd build
  	# cmake -DBUILD_SHARED_LIBS=OFF ..
  	# make
  	# make install
    6. Compile AltServer-Linux
        # git clone --recursive https://github.com/NyaMisty/AltServer-Linux
        # cd AltServer-Linux
	# mkdir build
	# cd build
        # make -f ../Makefile -j
  ```
