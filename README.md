# What ?
This repository contains FX3 firmware that acts as a bridge in the USB3 to JTAG device.
Due to licence restriction on Cypress SDK we cannot provide the modified source code directly.
Instead, we provide a patch to apply on SDK example to get the exact same code.
If you only want to set-up the usb3-to-JTAG device, we recommand you to use the released binary file (available in release/1.0/). To flash the FX3 from the binary firmware you need to follow all steps below except "Building the firmware".

# How ?

Before futher steps, please install the Cypress FX3 SDK (FX3_SDK_1.3.4_Linux.tar.gz) that contains required BSP and tools.
Use the link below for downloading. Once downloaded copy it inside this repository.
```
https://www.cypress.com/file/424271/download
```

## Building the firmware 

Use the following to build the FX3 firmware.
Note that we provide binary release so then you don't have to build from source.
If you want to use the release binary please go directly to next step (flashing firmware).

```
tar xzf FX3_SDK_1.3.4_Linux.tar.gz

tar xvf ARM_GCC.tar.gz

cd arm-2013.11/lib/gcc/arm-none-eabi/

mv 4.8.1/* ./

cd ../../../../

tar xvf fx3_firmware_linux.tar.gz

cd cyfx3sdk/util/elf2img

gcc elf2img.c -o elf2img

cd ../../../cyfx3sdk/firmware/slavefifo_examples

chmod +w -R slfifosync

patch -p1 < ../../../v0-1.patch

cd slfifosync

make clean

make FX3FWROOT=../../../../cyfx3sdk/ ARMGCC_INSTALL_PATH=../../../../arm-2013.11/ all

../../../../cyfx3sdk/util/elf2img/elf2img -vectorload yes -i cyfxslfifosync.elf -o cyfxslfifosync.img
```

If the makefile complain about arm-none-eabi-gcc not find, you need to install arm-none-eabi-gcc or add the SDK toolchain to your PATH. To add the SDK toolchain to you PATH on bash:
```
export PATH=/path/to/ez-usb-fx3/arm-2013.11/arm-none-eabi/bin/:$PATH
```

## Flashing the firmware

This section contains the required steps to flash the device using firmware update command from the FX3 bootloader.
When all the jumpers are closed on the FX3 board, the bootloader listen for USB commands. The firmware update is implemented by the tool download_fx3 that we need to install.

If it is not already done, extract the content of the downloaded archive (FX3_SDK_1.3.4_Linux.tar.gz)
and sub-archive. The 'cyusb_linux_1.0.5.tar.gz' contains required tool and library.
```
tar xzf FX3_SDK_1.3.4_Linux.tar.gz

tar xvf cyusb_linux_1.0.5.tar.gz
```

Then, set udev-rules and install needed libraries.
```
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
```

Now, compile the firmware update tool (dowload_fx3).
```
cd cyusb_linux_1.0.5/src

make
```

Then, flash the firmware using the following command.
```
./src/download_fx3 -t I2C -i ./release/1.0/cyfxslfifosync.img
```

If you are building from source.
```
./src/download_fx3 -t I2C -i ../cyfx3sdk/firmware/slavefifo_examples/slfifosync/cyfxslfifosync.img
```

# Credits
Author: 

* Giovanni Camurati  - EURECOM
* Nassim Corteggiani - Maxim Integrated
