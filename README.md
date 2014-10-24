Overview
========
Run ARM binaries from within a docker container on your x86_64 system. This can
assist with cross compiling ARM systems from within docker, or a variety of
other tasks.

[![ScreenShot](http://i.imgur.com/FJbYVVo.png?1)](https://drive.google.com/file/d/0B25hHW4ATym7MW9uc1R4MC0wZGM/edit?usp=sharing)


Assumption
----------
This assumes a docker host kernel with binfmt_misc support (module or built-in).

There are two pieces to this docker PoC. A data container (where we store our
images) and the actual emulation system image.

Installation
------------
1. add an ARM system image to `data/images` in this directory for later
mounting on loopback interface (once we're in the docker container)
  - alternatively you can grab a system tarball image and just cd into that
    (try one of these: http://archlinuxarm.org/platforms/armv7)

2. build the data image container (`make` inside `data/`) -- this stores your
system images in a separate container than the running system

3. run the data container once (if you don't understand why, see
https://docs.docker.com/userguide/dockervolumes/).

3. build the qemu-arm image (run `make` inside `qemu-arm/`).

Basically:

```
pushd data && make
docker run --name qemu-arm_data --entrypoint="/bin/true" -d qemu-arm-data
popd
pushd qemu-arm
make
popd
docker run --privileged -t -i --rm --volumes-from qemu-arm_data qemu-arm
```

Running
--------
now in the docker container shell:

If mounting a system image:
```
mkdir -p /chroot
mount -o loop /data/images/<your image>.bin /chroot
cd /chroot
cp /usr/bin/qemu-arm-static /chroot/usr/bin/qemu-arm-static
chroot . /bin/sh
```

Otherwise just cd into your system image folder, copy qemu-arm-static into
`chroot/usr/bin` and then `chroot . /bin/sh`.

Try running `uname -a` or running some files inside your chroot.

Troubleshooting
---------------
- Maybe your host docker kernel doesn't have binfmt_misc support. I build a
  custom boot2docker kernel for this
  [here](https://github.com/dweinstein/boot2docker/releases/tag/v1.2.1-binfmt).

- Try checking `update-binfmts --display` to see if qemu-arm-static is
  registered

- Make sure binfmts is mounted (`mount`) - if not `mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc`


Related reading
---------------
* binfmt-misc - https://www.kernel.org/doc/Documentation/binfmt_misc.txt
* docker volumes - https://docs.docker.com/userguide/dockervolumes/
* https://wiki.debian.org/QemuUserEmulation
