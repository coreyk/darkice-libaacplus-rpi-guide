# Compilation guide
## for darkice, libaacplus, et al on raspberry pi running raspbian jessie
to facilitate streaming audio from one of these turntables with USB audio codec output
- Audio Technica AT-LP120-USB (http://amzn.to/1TZvF2y)
- Ion TTUSB or Max (http://amzn.to/1Uv6KDY)


## Dependencies
```
sudo apt-get install libmp3lame-dev; sudo apt-get install autoconf; sudo apt-get install libtool; sudo apt-get install checkinstall; sudo apt-get install libssl-dev; apt-get install libasound2-dev; apt-get install libmp3lame-dev; apt-get install libpulse-dev
```

## Compiling libaacplus
```
wget http://tipok.org.ua/downloads/media/aacplus/libaacplus/libaacplus-2.0.2.tar.gz
tar -xzf libaacplus-2.0.2.tar.gz
cd libaacplus-2.0.2
./autogen.sh --with-parameter-expansion-string-replace-capable-shell=/bin/bash --host=arm-unknown-linux-gnueabi --enable-static
make
sudo make install
```

## Compliling libx264
```
cd /home/pi/src
git clone git://git.videolan.org/x264
cd x264
./configure --host=arm-unknown-linux-gnueabi --enable-static --disable-opencl
make
sudo make install
```

## Compiling libfaac
```
cd /home/pi/src
curl -#LO http://downloads.sourceforge.net/project/faac/faac-src/faac-1.28/faac-1.28.tar.gz
tar xzvf faac-1.28.tar.gz
cd faac-1.28
vi common/mp4v2/mpeg4ip.h
```
then change near line **126**:
```
#ifdef __cplusplus
extern "C" {
#endif
#ifndef _STRING_H
char *strcasestr(const char *haystack, const char *needle);
#endif
#ifdef __cplusplus
}
#endif
```
## Get darkice source
enable the raspbian source repo
```
vi /etc/apt/sources.list
```
uncomment the deb-src line
```
deb-src http://archive.raspbian.org/raspbian/ jessie main contrib non-free rpi
```
get the source
```
cd ~
mkdir src
cd src
apt-get source darkice
cd darkice-1.2
```

## Compiling darkice

```
./configure --with-aacplus --with-aacplus-prefix=/usr/local --with-pulseaudio --with-pulseaudio-prefix=/usr/lib/arm-linux-gnueabihf --with-lame --with-lame-prefix=/usr/lib/arm-linux-gnueabihf --with-alsa --with-alsa-prefix=/usr/lib/arm-linux-gnueabihf --with-jack --with-jack-prefix=/usr/lib/arm-linux-gnueabihf --with-faac --with-faac-prefix=/usr/local
make
make install
```

## Configure /etc/darkice.cfg
```
# this section describes general aspects of the live streaming session
[general]
duration        = 0        # duration of encoding, in seconds. 0 means forever
bufferSecs      = 1         # size of internal slip buffer, in seconds
reconnect       = yes       # reconnect to the server(s) if disconnected
realtime        = yes       # run the encoder with POSIX realtime priority
rtprio          = 3         # scheduling priority for the realtime threads

# this section describes the audio input that will be streamed
[input]
device          = hw:1,0  # OSS DSP soundcard device for the audio input
sampleRate      = 48000     # sample rate in Hz. try 11025, 22050 or 44100
bitsPerSample   = 16        # bits per sample. try 16
channel         = 2         # channels. 1 = mono, 2 = stereo

# this section describes a streaming connection to an IceCast2 server
# there may be up to 8 of these sections, named [icecast2-0] ... [icecast2-7]
# these can be mixed with [icecast-x] and [shoutcast-x] sections
[icecast2-0]
bitrateMode     = cbr
format          = aacp
bitrate         = 64
server          = localhost
port            = 8000
password        = SOURCE_PASSWORD
mountPoint      = listen
name            = Vinyl
description     = DarkIce on Raspberry Pi
url             = http://localhost
genre           = vinyl
public          = no
localDumpFile   = recording.m4a
```
