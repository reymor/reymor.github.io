---
layout: post
category: tutorials
---

Now Linux in recent versions (5.18 onwards this is around 2022) includes a new DRM driver for the Solomon OLED display controllers.

Here, we are going to explain how to use in the Beaglebone black.

![beagleboneblack_ssd1306_oled](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/solomon-ssd1306-bbb.png)

In the past, there was a driver using the `fbdev` subsystem. Nowadays, `fbdev` doesn't take any new drivers anymore. For this reason, this driver was for too long in `staging` directory. The idea here is to convert these drivers to use the `DRM` subsystem. There are many examples on how to connect this kind of display to the beaglebone using the `fbtft` driver. However, here we are going to use the new driver which is a DRM driver.

The official BeagleBoard kernel repository could be found [here](https://github.com/beagleboard/linux)

Here, we are going to connect the display to `i2c-2` into the beaglebone black.

| PIN   | Function | 
| ---   | ---      |
| P9.19 | I2C_SCL  |
| P9.20 | I2C_SDA  |

You need to be running a recent Linux kernel. Using the following command to determine which kernel you are running:

```bash
bone$ uname -a
Linux BeagleBone 6.1.80-ti-r34 #1bullseye SMP PREEMPT Fri Mar 22 11:06:21 UTC 2024 armv7l GNU/Linux
```
If you are running an older version like 5.10. You shall update it. To update to a recent kernel, ensure that your Bone is on the Internet and the run the following:

```bash
bone$ sudo apt install linux-image-6.1.80-ti-r34
bone$ sudo reboot
```
Once you have installed the kernel. You would need to modify the device tree with the following:

```C
&i2c2 {
    ssd1306: oled@3c {
        compatible = "solomon,ssd1306";
        reg = <0x3c>;
        solomon,width = <128>;
        solomon,height = <64>;
        solomon,com-invdir;
        solomon,page-offset = <0>;
    };
};
```

You could compile the device tree with the following command:

```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- am335x-boneblack.dtb
```

You shall compile the DRM modules, run the following the commands:

```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- modules_prepare
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- M=drivers/gpu/drm/solomon CONFIG_DRM_SSD130X=m CONFIG_DRM_SSD130X_I2C=m
```

This is going to generate `ssd130x.ko` and `ssd130x-i2c.ko` kernel modules.

Copy the generated modules and install them with the following command:

```bash
bone$ sudo modprobe drm_shmem_helper && sudo insmod ssd130x.ko && sudo insmod ssd130x-i2c.ko
```
