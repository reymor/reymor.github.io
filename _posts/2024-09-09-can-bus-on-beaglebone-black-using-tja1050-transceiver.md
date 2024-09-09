---
layout: post
category: tutorials
---

Here you can use these cheaps adapters that you can find online

![tja1050](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/tja1050.png)

The BBB has two internal CAN-Bus controller which are called `DCAN0` and `DCAN1`. Here, we are going to use `DCAN1` because the `DCAN0` interface is shared with I2C-Bus for cape-identification. `DCAN1` interface is multiplexed to the connector P9 pin 24 (CAN_RX --> UART1_TXD) and 26 (CAN_TX --> UART1_RXD).

![beagleboneblack_pins](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/beagleboneblack_pins.png)

## Configure BeagleBone Black pins

We could assign the CAN function temporary using the `config-pin` utility

```bash
sudo config-pin p9.24 can
sudo config-pin p9.26 can
```

## Enable CAN interface

CAN is implemented as a network in Linux. In other words, you can use similar utils to a common network. 

```bash
sudo ip link set can1 up type can bitrate 500000
sudo ifconfig can1 up
```

## Send and receive data

By default the Debian distro that comes with the BeagleBone has the [can-utils](https://github.com/linux-can/can-utils) installed.

```bash
cansend can1 123#DE.AD.BE.AF
```

```bash
candump can1
```

![can_send_recv](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/can_send_recv.png)
