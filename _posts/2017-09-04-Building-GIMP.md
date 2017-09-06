---
layout: post
title: Building GIMP
---


<div class="message">
  My notes on building GIMP
</div>


# Building GIMP 2.9.4

The [Official Docs](https://www.wiki.gimp.org/wiki/Hacking:Building) are helpful outline, but don't help the linking and version mismatch problems I've encountered. I `git checkout` the following tags:

1. gimp GIMP_2_9_4

## Order:

1. libmypaint
1. babl
1. gexiv2 (optional)
1. gegl
1. gimp

## Flags:

```
MAKE="make -j -l 7.5" # for an 8 core system
INSTALL_PREFIX=/usr/local/stow/gimp-2.9 # customized for stow
export PKG_CONFIG_PATH=$INSTALL_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH
export LD_LIBRARY_PATH=$INSTALL_PREFIX/lib:$LD_LIBRARY_PATH
```

## Dependencies (on a Debian sbuild):

```
build-essential
automake
intltool
libtool
make

#gtk-doc-tools
libbz2-dev
libgexiv2-dev
libglib2.0-dev
libjpeg62-turbo-dev
libjson-c-dev
libjson-glib-dev
liblcms2-dev
liblzma-dev
libpng-dev
librsvg2-dev
libtiff5-dev
python-cairo-dev
python-gtk2-dev
xsltproc
zlib1g
```

## Building each package

```
./autogen.sh --prefix=$INSTALL_PREFIX --disable-gtk-doc
$MAKE install
```
