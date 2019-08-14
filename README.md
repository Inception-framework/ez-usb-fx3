# What ?
This repository contains FX3 firmware that acts as a bridge in the USB3 to JTAG device.

# How ?

Before futher steps, please install the Cypress FX3 SDK that contains required BSP and tools.

```
https://www.cypress.com/file/424271/download
```

## Building the firmware 

Use the following to build the FX3 firmware.
Note that we provide binary release so then you don't have to build from source.
Otherwise, set the FX3FWROOT and ARMGCC_INSTALL_PATCH variable to the correct location in the FX3 SDK.

```
cd /path/to/FX3_SDK_1.3.4_Linux/fx3_firmware_linux/cyfx3sdk/firmware/slavefifo_examples/slfifoasync

git apply /path/to/ez-usb-fx3/v0-1.patch

make clean

make FX3FWROOT=/path/to/FX3_SDK_1.3.4_Linux/fx3_firmware_linux/cyfx3sdk ARMGCC_INSTALL_PATH=/path/to/FX3_SDK_1.3.4_Linux/arm-2013.11 all
```

## Flashing the firmware

Before flashing make sure that all jumpers are closed.

```
cd /path/to/FX3_SDK_1.3.4_Linux/cyusb_linux_1.0.5/src

make

./download_fx3 -t I2C -i /path/to/ez-usb-fx3/release/1.0/SlaveFifoSync.elf
```
