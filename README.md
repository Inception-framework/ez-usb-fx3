# What ?
This repository contains FX3 firmware that acts as a bridge in the USB3 to JTAG device.
Due to licence restriction on Cypress SDK we cannot provide the source code directly.
Instead, we provide a patch to apply on SDK example to get the exact same code.
If you onlt want to set-up the usb3-to-jtag device, we recommand you to use the release binary file.

# How ?

Before futher steps, please install the Cypress FX3 SDK that contains required BSP and tools.
Use the link below for downloading. Once downloaded copy it inside this repository.
```
https://www.cypress.com/file/424271/download
```

## Building the firmware 

Use the following to build the FX3 firmware.
Note that we provide binary release so then you don't have to build from source.
If you want to use the release binary please go directly to next step (flashing firmware).
Otherwise, set the FX3FWROOT and ARMGCC_INSTALL_PATCH variable to the correct location in the FX3 SDK.

```
tar xzf FX3_SDK_1.3.4_Linux.tar.gz

tar xvf ARM_GCC.tar.gz

cd arm-2013.11/lib/gcc/arm-none-eabi/

mv 4.8.1/* ./

cd ../../../../

tar xvf fx3_firmware_linux.tar.gz

cd cyfx3sdk/util/elf2img

gcc elf2img.c -o elf2img

cd cyfx3sdk/firmware/slavefifo_examples

chmod +w -R slfifosync

patch -p1 < ../../../v0-1.patch

cd slfifosync

make clean

make FX3FWROOT=../../../../cyfx3sdk/ ARMGCC_INSTALL_PATH=../../../../arm-2013.11/ all

../../../../cyfx3sdk/util/elf2img/elf2img -vectorload yes -i cyfxslfifosync.elf -o cyfxslfifosync.img
```

## Flashing the firmware

Before flashing make sure that all jumpers are closed.

```
tar xvf cyusb_linux_1.0.5.tar.gz

cd cyusb_linux_1.0.5/

# if it complains about qt4 library, we don't care
# we only use this script to install shared library
# and set udev rules to allow FX3 device access
# you could do it manually using ldconfig and copying
# configs/88-cyusb.rules in /etc/udev/rules.d/
# 
sudo ./install.sh

# refresh rules, sometines you may need to unplug the device
sudo udevadm control --reload-rules && sudo udevadm trigger

cd src

make

cd ..

./src/download_fx3 -t I2C -i ../cyfx3sdk/firmware/slavefifo_examples/slfifosync/cyfxslfifosync.img
```

or

```
./src/download_fx3 -t I2C -i ./release/1.0/cyfxslfifosync.img
```

# Credits
Author: 

* Giovanni Camurati  - EURECOM
* Nassim Corteggiani - Maxim Integrated
