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

## TODO / Special Features
- [x] Track upstream (AltServer-Windows) develop branch (i.e. Beta version)
- [x] Support Offline Anisette Data Generation (i.e. without Sideloadly)
  - Finsihed, please run [alt_anisette_server](https://hub.docker.com/r/nyamisty/alt_anisette_server) & use `ALTSERVER_ANISETTE_SERVER` to specify custom server URL
- [x] Support Wi-Fi Refresh
  - [netmuxd](https://github.com/jkcoxson/netmuxd) now supports network devices (needs version > v0.1.1, be sure to check pre-release)
    - Download `netmuxd`, stop the original `usbmuxd`, and run `netmuxd` before running `AltServer-Linux`
    - ~If netmuxd does not work, please try using special env var `ALTSERVER_NO_SUBSCRIBE`. Enabling this would disable **auto-refresh when plugged-in** of USB devices~

----

## Build Instruction
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
        docker run -it alpine
    2. Install dependencies:
  	# cd
  	# apk add git g++ clang cmake make openssl-dev util-linux-dev cpprestsdk-dev libzip-dev boost-static boost-dev python3
    3. Install corecrypto
  	# git clone https://github.com/lewhfree/corecrypto
  	# cd corecrypto
  	# mkdir build
  	# cd build
  	# cmake ..
  	# make
  	# make install
  	# cd ~
  	# rm -rf corecrypto
    4. Compile AltServer-Linux
  	# git clone --recursive https://github.com/lewhfree/AltServer-Linux altserver
  	# cd altserver
  	# mkdir build
  	# cd build
  	# make -f ../Makefile
  ```
