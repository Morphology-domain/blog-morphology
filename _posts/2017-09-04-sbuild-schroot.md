---
layout: post
title: sbuild off of snapshots
tags: Debian chroot
---


<div class="message">
  My notes on quickly configuring a 32-bit schroot in a 64-bit system.
</div>

This uses an LVM2 volume.

# Steps

Without `mk-sbuild`.

```
dname=Chroot_stretch_i386
apt install sbuild
lvcreate -L 10G ubuntu-vg -n $dname
mkfs -t ext2 /dev/ubuntu-vg/$dname
mount /dev/ubuntu-vg/$dname /mnt
sbuild-createchroot --arch=i386 --include=less,dialog stretch /mnt http://httpredir.debian.org/debian
umount /mnt
```

Your logical volume now contains a minimal Debian. Make an appropriate /home for your user account.

For development, consider:

1. For github: include git, openssh-client, and copy your .ssh keys
1. For gcloud: include git, curl, ca-certificates, gnupg2, and follow install instructions
1. For AWS, your PEM

Take a snapshot:

`lvcreate -s -n Chroot_ruby --size 5G /dev/ubuntu-vg/$dname`

You may now install `ruby-dev`, or follow the instructions for `nodejs`, etc.

## Resulting config file

```
[ruby]
type=block-device
device=/dev/ubuntu-vg/Chroot_ruby
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
