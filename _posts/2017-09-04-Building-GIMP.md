---
layout: post
title: Building GIMP
tags: Debian GIMP
---


<div class="message">
  My notes on building GIMP
</div>


# Building GIMP 2.9.4

The [Official Docs](https://www.wiki.gimp.org/wiki/Hacking:Building) are helpful outline, but don't help the linking and version mismatch problems I've encountered.

## Order:

1. libmypaint
   git checkout v1.3.0
1. babl
1. gegl
1. gimp
   git checkout GIMP_2_9_4

## Dependencies (on a Debian sbuild):

On an sbuild, I think you'll have to manually create /usr/local/stow with
ownership root:staff and mode 0775.

Pull in the dependencies for the (older) package with `apt-get build-dep gimp`

```
sed 's/\s*[#].*//' <<- EOF | xargs apt -y install
	build-essential
	automake
	git
	intltool
	libtool
	make
	pkg-config

	libglib2.0-dev
	libgtk2.0-dev

	# libmypaint
	libjson-c-dev

	# gegl
	libjson-glib-dev
	# libjpeg62-turbo-dev # Debian
	libjpeg-turbo8-dev # Ubuntu
	libpng-dev

	# gimp
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
# MAKE="make -j -l 7.5" # for an 8 core system, but prone to failure, esp. on GIMP
MAKE=make # I even had problems with -j on a 2-core system
INSTALL_PREFIX=/usr/local/stow/gimp-2.9 # customized for stow
export PKG_CONFIG_PATH=$INSTALL_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH
export LD_LIBRARY_PATH=$INSTALL_PREFIX/lib:$LD_LIBRARY_PATH
export PATH=$INSTALL_PREFIX/bin:$PATH
```


### For each package

```
./autogen.sh --prefix=$INSTALL_PREFIX --disable-gtk-doc
# libmypaint also needs:
./configure --prefix=$INSTALL_PREFIX --disable-gtk-doc
$MAKE install
```

## Runtime dependencies

Your build can be packed and transferred (this isn't packaged like dpkg) so
long as at runtime you've installed these requirements not included in the
Ubuntu base:

```
sed 's/\s*[#].*//' <<- EOF | xargs apt -y install
	python-gtk2
EOF
```
