# ARM Instruction Set of Opcode

All ARM instruction codes are 32 bits in ARMv5 architecture

`0004 e3a0` ~ Writes value 4 in r0 register

`1005 e3a0` ~ Writes value 5 in r1 register

# Executing in QEMU

When the ARM processing is reset, it starts executing from address 0x0

On the connex board a 16MB Flash is located at address 0x0

Refer Picture: flash-ram-mm.png

The instructions present in the beginning of the Flash will be executed.

When qemu emulates the connex board, a file has to be specified which will be treated file as Flash memory

The Flash file format is very simple

To get the byte from address X in the Flash, qemu reads the byte from offset X in the file. In fact, this is the same as the binary file format.

To test the program on the emulated Gumstix connex board, we need to first create a 16MB file representing the Flash

`$ dd if=/dev/zero of=flash.bin bs=4096 count=4096`

`$ qemu-system-arm -M connex -pflash flash.bin -serial /dev/null -nographic`

Using hexedit i can update values accordingly
