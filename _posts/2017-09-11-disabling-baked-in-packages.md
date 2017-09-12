---
layout: post
title: Disabling baked-in packages in Debian and Ubuntu
tags: apt localizing
---


<div class="message">
  My notes on localizing a system
</div>

Evolution and Zeitgeist are baked into Ubuntu. Purging with `apt-get` is
unwise, as moving parts in the desktop depend on them.

For `/usr/bin/zeitgeist*`:
```
# Disabled with:
dpkg-divert --add --rename --divert /usr/bin/zeitgeist-daemon.real /usr/bin/zeitgeist-daemon

# You can enable with:
dpkg-divert --rename --remove /usr/bin/zeitgeist-daemon
```
You can replace the binaries with this file.

For `/usr/lib/evolution/evolution*`:
```
# Disabled with:
for f in /usr/lib/evolution/evolution*
do dpkg-divert --add --rename --divert $f.disabled $f
done

# You can enable with:
for f in /usr/lib/evolution/evolution*.disabled
do dpkg-divert --rename --remove ${f%.disabled}
done
```
You can replace the binaries with this file.
