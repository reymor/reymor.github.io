---
layout: post
category: tutorials
---

[STM32F429I-DISC](https://www.st.com/en/evaluation-tools/32f429idiscovery.html) is an evaluation kit based on STM32F429ZIT6 high-perfomance MCUs with Cortex-M4 core (MMU-less).

![stm32f429_running_linux](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/stm32f4-linux.gif)

There are at least four elemtents of embedded Linux which are:

- **Toolchain**: This is the cross compiler and other tools needed to create code for our target. We are going to [armv7m--uclibc--stable-2025.08-1](https://toolchains.bootlin.com/downloads/releases/toolchains/armv7m/available_toolchains/) from Bootlin.
- **Bootloader**: This program initilzies the board/peripherals. We are going to use U-Boot *2025.10*.
- **Kernel**: The heart of the system, we are going to use Linux *6.17*.
- **Root filesytem**: This contains the librarteis and programs that are run once the kernel has completed its init. In this case we are going to use a *ramdiskfs* based on busybox *1.36*.

Mainline Linux has support for MMU-less systems like this microntroller. In addition, it includes support for this evk. This is 

## Table of contents
- [Toolchain](#toolchain-ins)
- [Bootloader](#bootloader)
- [Kernel](#kernel)
- [Root filesytem](#rootfs)

## Toolchain(#toolchain)

## Bootloader(#bootloader)

## Kernel(#kernel)

## Root filesytem(#rootfs)

