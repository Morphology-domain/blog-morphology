---
layout: post
title: Shortcuts to Debian->Ubuntu recovery using schroot
tags: sbuild schroot Debian Ubuntu
---


<div class="message">
  My notes on localizing a system
</div>

This automates some of the recovery in case of a borked initramfs or crypto
partition. Pseudo-filesystems, necessary for some recovery, are automatically
mounted. Makes backup and recovery quicker.

`schroot` is already on your system if you sbuild. I take the extra step to
install a small Debian partition with a few recovery tools. It can share /boot
/home /tmp and swap with Ubuntu. This isn't a problem if they share the same
bootloader. If that happens, these directions will be stale.

### Profile under /etc/schroot

/etc/schroot/Recovery contains:

| /etc/schroot/Recovery/ | modifications
|---|-
| copyfiles    | unchanged
| fstab        | modified to pull any Ubuntu filesystems in, such as /var
| nssdatabases | empty -- this is important!

nssdatabases will nuke your precious /etc/passwd and others. Warning: We're
using `schroot` for other than it's intended purpose. The danger will let you
feel alive.

### schroot config

```
[Recovery]
type=block-device
device=/dev/UbuntuRoot
profile=Recovery
groups=adm,staff
root-groups=root
```
