---
layout: post
title:  "Bluetooth"
excerpt: "A write up on mocking bluetooth devices and connecting to them"
date: 2022-03-05 02:15:20 -0500
---

gh repo clone bluez/bluez
read README.md and HACKING
i need to build with the `--enable-testing` flag
which according to the [stack overflow](https://stackoverflow.com/questions/20316119/creating-virtual-bluetooth-device-service-and-let-executing-computer-discover-us) will help me create some mock ble devices

building by following HACKING i need to run from within the source code repo
just dont do the `sudo make install` as maintainer mode will build it locally

```bash
# you may have to install some dependencies.
# just read the output of ./bootstrap-configure to find out what you're missing
./bootstrap-configure
# rst2man is  python-doctools
pacman -S python-docutils
# make sure the enable-testing flag is on in the bootstrap-configure. on a fresh clone it should be
make
```

why the fuck doesnt this exist?
```
In file included from tools/mesh-cfgclient.c:26:
./ell/ell.h:1:10: fatal error: ell/util.h: No such file or directory
    1 | #include <ell/util.h>
      |          ^~~~~~~~~~~~
compilation terminated.
make[1]: *** [Makefile:7274: tools/mesh-cfgclient.o] Error 1
make: *** [Makefile:4310: all] Error 2
```

