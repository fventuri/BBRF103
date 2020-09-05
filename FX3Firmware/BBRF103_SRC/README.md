# BBRF103 FX3 firmware

## How to build the firmware image from source

Please note: these instructions are for Linux, but I think the steps on Windows and MacOS are not very different.

- Download the latest EZ-USB FX3 Software Development Kit from Cypress: <https://www.cypress.com/documentation/software-and-drivers/ez-usb-fx3-software-development-kit>
- The Linux install contains five files; the only one you need to compile the firmware is ```fx3_firmware_linux.tar.gz```
- Extract the contents of ```fx3_firmware_linux.tar.gz``` somewhere; for instance I used ```/opt/cypress``` (you'll need to be root to be able to install it in this location):
```
mkdir /opt/cypress
tar xzf fx3_firmware_linux.tar.gz -C /opt/cypress
```
- Compile the elf2img binary (it will be needed later):
```
cd /opt/cypress/cyfx3sdk/util/elf2img
gcc -O -Wall elf2img.c -o elf2img
cd -
```
- Download the latest version of the GNU Arm Embedded Toolchain from ARM: <https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm>
- Extract the contents of ```gcc-arm-none-eabi-9-2020-q2-update-x86_64-linux.tar.bz2``` somewhere; for instance I used ```/opt/arm``` (you'll need to be root to be able to install it in this location):
```
mkdir /opt/arm
tar xjf gcc-arm-none-eabi-9-2020-q2-update-x86_64-linux.tar.bz2 -C /opt/arm
ln -s gcc-arm-none-eabi-9-2020-q2-update gcc-arm-none-eabi
```
- add the directory for the GCC ARM toolchain to your PATH; also we'll need a couple more environment variables (you may want to add these lines to your '.profile' or '.bash_profile' if you plan to work on building firmware in the future):
```
PATH=$PATH:/opt/arm/gcc-arm-none-eabi/bin
export ARMGCC_INSTALL_PATH=/opt/arm/gcc-arm-none-eabi
export ARMGCC_VERSION=9.3.1
```
(don't forget tu run ```. ~/.profile``` or ```. ~/.bash_profile``` if you just added them to your profile)
- build the firmware
  - you may need to edit ```makefile```, and change the location of ```FX3FWROOT``` at the beginning if you installed the EZ-USB FX3 SDK in a different location
  - also in the ```makefile```, commenting the variable ```CYCONFOPT``` (or changing it to ```fx3_debug```) will produce a debug firmware
```
make
```
- convert the resulting ELF to an FX3 bootable image using the 'elf2img' tool we built earlier:
```
/opt/cypress/cyfx3sdk/util/elf2img/elf2img -i sdrx3.elf -o sdrx3.img
```
