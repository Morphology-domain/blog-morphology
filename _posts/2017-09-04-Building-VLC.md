---
layout: post
title: Building GIMP
---


<div class="message">
  My notes on building VLC
</div>


# Building GIMP 2.9.4

The [Official Docs](https://wiki.videolan.org/UnixCompile/) are helpful outline, but don't help the linking and version mismatch problems I've encountered. I `git checkout` the following tags:

1. vlc 2.2.0-git

## Order:

1. ffmpeg
1. vlc

## Flags:

```
MAKE="make -j -l 7.5" # for an 8 core system
INSTALL_PREFIX=/usr/local/stow/vlc-2.2 # customized for stow
export PKG_CONFIG_PATH=$INSTALL_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH
export LD_LIBRARY_PATH=$INSTALL_PREFIX/lib:$LD_LIBRARY_PATH
```

## Dependencies (on a Debian sbuild):

```
sed -n '/^$/!p' << EOF | xargs apt install
	build-essential

	automake
	autopoint
	gettext
	libtool
	pkg-config


EOF
```

### Building FFMpeg

git://git.ffmpeg.org/ffmpeg.git


#### Dependencies

```
sed -n '/^\s*$/!p' << EOF | xargs apt install
	build-essential

	autoconf
	automake
	libtool
	pkg-config

	texinfo
	wget

	libass-dev
	libfreetype6-dev
	libsdl2-dev
	libtheora-dev
	libva-dev
	libvdpau-dev
	libvorbis-dev
	libxcb1-dev
	libxcb-shm0-dev
	libxcb-xfixes0-dev
	zlib1g-dev

	yasm

	libx264-dev
	libx265-dev
	libmp3lame-dev
	libopus-dev
	libvpx-dev
EOF
```

`--enable-shared `

After building `ffmpeg`, do `stow -S` and `ldconfig`.

... Then ...

git://github.com/mstorsjo/fdk-aac

... And ...

`apt-get build-dep vlc`


## Building each package

```
./configure --prefix=$INSTALL_PREFIX --disable-gtk-doc
$MAKE install
```
