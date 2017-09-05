---
layout: post
title: sbuild to block device
---


<div class="message">
  My notes on quickly configuring a 32-bit schroot in a 64-bit system.
</div>

This uses an LVM2 volume.

# Steps

Without `mk-sbuild`.

```
dname=Chroot_Stretch_i386
apt install sbuild
lvcreate -L 10G FastVG -n $dname
mkfs -t ext4 /dev/FastVG/$dname
mount /dev/FastVG/$dname /mnt
sbuild-createchroot --arch=i386 --include=less,dialog stretch /mnt http://httpredir.debian.org/debian
umount /mnt
```

## Resulting config file

```
[stretch]
type=block-device
device=/dev/FastVG/$dname
description=Debian Stretch (stable) 32-bit
aliases=stable
profile=desktop
personality=linux32
groups=adm,staff
root-groups=root
```

## Debian customizations

`desktop/copyfiles`:
```
## on Debian, /var/lib/dbus/machine-id links to this:
/etc/machine-id
```

`desktop/fstab`:
```
/media         /media          none    rw,bind         0        0
```
