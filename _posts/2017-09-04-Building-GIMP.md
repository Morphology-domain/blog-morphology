---
layout: post
title: Building GIMP
---


<div class="message">
  My notes on building GIMP
</div>


# Building GIMP 2.9.4

The [Official Docs](https://www.wiki.gimp.org/wiki/Hacking:Building) are helpful outline, but don't help the linking and version mismatch problems I've encountered. I `git checkout` the following tags:

## Order:

1. libmypaint
1. babl
1. gegl
1. gimp

## Dependencies (on a Debian sbuild):

Pull in the dependencies for the (older) package with `apt-get build-dep gimp`

```
sed -n '/\s*[#]/!p' <<- EOF | xargs echo apt install
	build-essential
	automake
	intltool
	libtool
	make
	pkg-config

	libglib2.0-dev
	libgtk2.0-dev

	# libmypaint
	# git checkout v1.3.0
	libjson-c-dev

	# gegl
	libjson-glib-dev
	libjpeg62-turbo-dev
	libpng-dev

	# gimp
	# git checkout GIMP_2_9_4
	libbz2-dev
	libgexiv2-dev
	liblcms2-dev
	liblzma-dev
	libtiff5-dev
	python-cairo-dev
	python-gtk2-dev
	xsltproc
EOF
```

## Compilation:

```
xargs -n 1 git clone <<- EOF
	git://github.com/mypaint/libmypaint
	git://git.gnome.org/babl
	git://git.gnome.org/gegl
	git://git.gnome.org/gimp
EOF
```
### Flags:

```
# MAKE="make -j -l 7.5" # for an 8 core system
MAKE=make # I had problems with -j on a 2-core system
INSTALL_PREFIX=/usr/local/stow/gimp-2.9 # customized for stow
export PKG_CONFIG_PATH=$INSTALL_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH
export LD_LIBRARY_PATH=$INSTALL_PREFIX/lib:$LD_LIBRARY_PATH
```


### For each package

```
./autogen.sh --prefix=$INSTALL_PREFIX --disable-gtk-doc
# libmypaint also needs:
./configure --prefix=$INSTALL_PREFIX --disable-gtk-doc
$MAKE install
```
