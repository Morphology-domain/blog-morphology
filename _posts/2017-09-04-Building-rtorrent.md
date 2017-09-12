---
layout: post
title: Building libtorrent and rtorrent
tags: Debian
---


<div class="message">
  My notes on building command-line BitTorrent
</div>


# Starting from the repo

```
https://github.com/rakshasa/libtorrent
https://github.com/rakshasa/rtorrent
```
`apt-get build-dep libtorrent`

## Dependencies (on a Debian sbuild):

```
build-essential

autoconf
libcppunit-dev
libtool
pkg-config

libncurses-dev
libncursesw5-dev
libssl-dev
zlib1g-dev
```

## Config

Refer to the [Official wiki](https://github.com/rakshasa/rtorrent/wiki/CONFIG-Template), and [FAQ](https://github.com/rakshasa/rtorrent/wiki/Common-Tasks-in-rTorrent).
