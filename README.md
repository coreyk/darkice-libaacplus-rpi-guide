# Compilation guide
## for darkice, libaacplus, et al on raspberry pi running raspbian jessie

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
## Compiling darkice

```
./configure --with-aacplus --with-aacplus-prefix=/usr/local --with-pulseaudio --with-pulseaudio-prefix=/usr/lib/arm-linux-gnueabihf --with-lame --with-lame-prefix=/usr/lib/arm-linux-gnueabihf --with-alsa --with-alsa-prefix=/usr/lib/arm-linux-gnueabihf --with-jack --with-jack-prefix=/usr/lib/arm-linux-gnueabihf --with-faac --with-faac-prefix=/usr/local
```
