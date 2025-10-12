---
layout: post
category: tutorials
---

[STM32F429I-DISC](https://www.st.com/en/evaluation-tools/32f429idiscovery.html) is an evaluation kit based on STM32F429ZIT6 high-perfomance MCUs with Cortex-M4 core (MMU-less). Mainline Linux has support for MMU-less systems like this microntroller. In addition, it includes support for this evk.

![stm32f429_running_linux](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/stm32f4-linux.gif)

There are at least four elemetnts of embedded Linux which are:

- **Toolchain**: This is the cross compiler and other tools needed to create code for our target. We are going to [armv7m--uclibc--stable-2025.08-1](https://toolchains.bootlin.com/downloads/releases/toolchains/armv7m/available_toolchains/) from Bootlin.
- **Bootloader**: This program initilzies the board/peripherals. We are going to use U-Boot *2025.10*.
- **Kernel**: The heart of the system, we are going to use Linux *6.17*.
- **Root filesytem**: This contains the librarteis and programs that are run once the kernel has completed its init. In this case we are going to use a *ramdiskfs* based on busybox *1.36*.

## Table of contents
- [Toolchain](#toolchain)
- [Bootloader](#bootloader)
- [Kernel](#kernel)
- [Root filesytem](#rootfs)
- [Booting the board](#boot)
- [References](#ref)

## [Toolchain](#toolchain)

Obtaining a toolchain can be as simple as downloading and installing a *TAR* file or it can be as complex as building the whole thing from source code, check [1] for more info. An standard GNU toolchain consists of three at least of three components:

- Binutils
- GCC
- C library (glib, musl, uClib-ng)

Here, we are going to use just the [armv7m--uclibc--stable-2025.08-1](https://toolchains.bootlin.com/downloads/releases/toolchains/armv7m/available_toolchains/) from Bootlin.

```bash
wget https://toolchains.bootlin.com/downloads/releases/toolchains/armv7m/tarballs/armv7m--uclibc--stable-2025.08-1.tar.xz
```

## [Bootloader](#bootloader)

U-Boot supports a good number of processor architecutures and it is ubiquitous these days.

```bash
git clone https://github.com/u-boot/u-boot.git -b v2025.10

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- stm32f429-discovery_defconfig

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- -j 8
```

Once you build the U-boot, you could use [st-flash](https://github.com/stlink-org/stlink) util to flash the MCU.

```
st-flash write u-boot.bin 0x08000000
```

The idea here is to use [XMODEM/YMODEM](https://en.wikipedia.org/wiki/YMODEM) protocol to transfer the kernel, dtb and ramdisk to the external SDRAM.

## [Kernel](#kernel)

```bash
git clone https://github.com/torvalds/linux -b v6.17

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- stm32_defconfig

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- -j 8 uImage LOADADDR=0x90000000

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- dtbs
```

By default `stm32_defconfig` is configured to be used as XIP (eXecute-In-Place). However, in this case the idea is to load the kernel into external RAM, so you need to disable that. You could disable by invoking `menuconfig`. 

From here, you got: 

- `arch/arm/boot/uImage` --> Kernel with U-Boot header
- `arch/arm/boot/dts/st/stm32f429-disco.dtb` --> Device tree blob for this board

## [Root filesytem](#rootfs)

To create a minimal root filesystem, you need the following components:

- **init**: This is the program that starts everything off.
- **shel**: Provides a command prompt and executes shell scripts.

So, here we use busybox:

```bash
git clone https://github.com/mirror/busybox.git -b 1_36_1

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- defconfig

make ARCH=arm CROSS_COMPILE=arm-buildroot-uclinux-uclibcgnueabi- -j 8
```

Here, we are going to use a minimal *ramfs*. When you are working with a MMU-less device, this means that the kernel is not able to support regular *ELF* files. So, this support a format called Binary flat format (BINMFT_FLAT). So, there is a tool called [elf2flt](https://github.com/uclinux-dev/elf2flt). You don't need to worry about this because it is already include in the toolchain. However, it is interesting if you want to get more details about how everything works.

```bash
mkdir -p rootfs/{bin,sbin,etc,proc,sys,usr/{bin,sbin},dev,tmp}
cp busybox rootfs/bin/
cd rootfs/bin
for i in sh ls cat mount echo dmesg ps; do ln -s busybox $i; done
```

Minimal init
```bash
cat > rootfs/init <<'EOF'
#!/bin/sh
mount -t proc none /proc
mount -t sysfs none /sys
echo "=== Minimal rootfs started ==="
exec /bin/sh
EOF
chmod +x rootfs/init
```

Device nodes
```bash
sudo mknod rootfs/dev/console c 5 1
sudo mknod rootfs/dev/null c 1 3
```

Pack into a cpio archive

```bash
cd rootfs
find . -print0 | cpio --null -ov --format=newc --owner root:root  | gzip -9 > ../rootfs.cpio.gz
```

Create a ramdisk with a U-boot header

```bash
mkimage -A arm -O linux -T ramdisk -a 0x90300000 -e 0x90300000 -n "Tiny ARM Initramfs" -d rootfs.cpio.gz uInitrd
```

## [Booting the board](#boot)

At this point you need:

- `uInitrd`
- `uImage`
- `stm32f429-disco.dtb`

Here, as we said before we are going to use the builtin YMODEM functionality in U-boot. So, basically, we did this:

Load *kernel*, here you could use minicom or any YMODEM complainant tool.
```
loady 0x90000000
```

Load *dtb*
```
loady 0x90200000
```

Load *ramdisk*
```
loady 0x90300000
```

Last step, boot the board
```
boot 0x90000000 0x90300000 0x90200000
```

Remember that your bootargs variable shall be something like this:

```
bootargs=console=ttySTM0,115200 root=/dev/ram0 rdinit=/init
```

In addition, you could `saveenv` and also help with some auxiliary variables in order to help you in load faster the componets.

## [References](#ref)

1. [Toolchain Options in 2023: What's New in Compilers and Libcs? - Bernhard Rosenkränzer, BayLibre](https://www.youtube.com/watch?v=Vgm3GJ2ItDA)