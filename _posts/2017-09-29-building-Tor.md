---
layout: post
title: Building Tor
tags: Debian Tor
---


<div class="message">
  My notes on building Tor for distribution
</div>


# Building Tor 0.3.1.7

[Officially](https://www.torproject.org/docs/tor-doc-unix.html.en)

## Order:

1. libevent
   git checkout release-2.1.8-stable
1. tor
   git checkout tor-0.3.1.7

## Dependencies (on a Debian sbuild):

On an sbuild, I think you'll have to manually create /usr/local/stow with
ownership root:staff and mode g+rwXs.

```
sed 's/\s*[#].*//' <<- EOF | xargs apt -y install
	build-essential
	automake
	git
	libtool
	
	# libevent
	python

	# tor
	libssl-dev
	zlib1g-dev
EOF
```

## Compilation:

```
xargs -n 1 git clone <<- EOF
	git clone https://github.com/libevent/libevent
	git clone https://git.torproject.org/tor
EOF
```

### Flags:

```
MAKE="make -j -l 7.5" # for an 8 core system
INSTALL_PREFIX=/usr/local/stow/tor # customized for stow, see above
export PKG_CONFIG_PATH=$INSTALL_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH
export LD_LIBRARY_PATH=$INSTALL_PREFIX/lib:$LD_LIBRARY_PATH
export PATH=$INSTALL_PREFIX/bin:$PATH
```


### For each package

libevent might benefit from `--disable-static`

```
./autogen.sh	--prefix=$INSTALL_PREFIX --disable-asciidoc
./configure	--prefix=$INSTALL_PREFIX --disable-asciidoc
$MAKE install
```

# Running in a container

If using `chroot`, you likely should create the service user outside of it:

```
sudo adduser --system --home=/var/lib/tor --no-create-home --shell=/bin/false \
 --group debian-tor
```

```
debian_mirror='http://httpredir.debian.org/debian'
debootstrap --verbose --include=gnupg --resolve-deps \
  jessie /mnt "$debian_mirror"
```

Follow the (official instructions)[https://www.torproject.org/docs/debian.html.en].
