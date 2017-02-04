---
date: '2016-12-12 06:18 +0100'
published: true
title: Stream Internet Radio Station
category:
  - Linux
---
This article covers the steps in setting up an internet radio station on a debian system.



```bash
apt-get update
apt-get upgrade
apt-get install apt-get install vim gcc make zip build-essential pkg-config libxml2-dev icecast2 ezstream
sudo su
wget http://downloads.xiph.org/releases/ogg/libogg-1.3.1.tar.gz
tar xf libogg-1.3.1.tar.gz
cd libogg-1.3.1/
./configure
make && make install
wget http://download.xiph.org/releases/vorbis/libvorbis-1.3.3.tar.gz
tar xf libvorbis-1.3.3.tar.gz
cd libvorbis-1.3.3
./configure
make && make install
wget http://downloads.xiph.org/releases/libshout/libshout-2.3.1.tar.gz
tar xf libshout-2.3.1.tar.gz
cd libshout-2.3.1
./configure
make && make install
wget http://downloads.xiph.org/releases/ezstream/ezstream-0.5.6.tar.gz
tar xf ezstream-0.5.6.tar.gz
cd ezstream-0.5.6
./configure
make && make install
mkdir ~/mp3
cd mp3
vim playlist.m3u // add files with path
sudo cp /usr/share/doc/ezstream/examples/ezstream_mp3.xml ~/mp3/ezstream_mp3.xml
sudo vim /root/ezstream_mp3.xml // edit the xml file for your purps
sudo chmod 644 ezstream_mp3.xml
sudo screen -S "stream"
ezstream -c ezstream_mp3.xml
```
