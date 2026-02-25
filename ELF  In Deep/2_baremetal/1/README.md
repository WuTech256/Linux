# Setting up ARM Lab

QEMU is a machine emulator capable of emulating various machines including ARM based machine

We can write ARM assembly programs, compile them using GNU toolchain and execute and test them in QEMU

# Installing required packages

$ sudo apt install gcc-arm-none-eabi

$ dpkg -L gcc-arm-none-eabi

$ sudo apt install qemu-system-arm

$ qemu-system-arm -M help

# Connex Board

Using QEMU we will be emulating Connex board from Gumstix

+ Architecture: ARMv5TE
+ Arm Core: PXA255
+ SoC: PXA255
