# Changing one characters in ELF file

### Build the userspace ELF binary from source
$ make userspace

### Run the original program to verify it works
./userspace

### Open the raw ELF binary in vi (Not recommended)
$ vi ./userspace

### View the first bytes of the ELF file in a readable hexdump format (grouped by 1 byte)
$ xxd -g 1 ./userspace | head -5

### Show a "plain" hex dump: continuous hex bytes, no addressses or ASCII
$ xxd -p userspace | head -5

### Save the complete plain hex dump to a text file for editing
$  xxd -p userspace > hexdump.plain

###  Check file type (should be ASCII text)
$ file ./hexdump.plain

### Edit the hex manually - change 2 hex digits to modify 1 byte
$ vi ./hexdump.plain

### Convert the modified hex dump back to a binary ELF file (reverse mode)
$ xxd -p -r hexdump.plain > hexdump.elf

### Verify the output is a valid ELF binary
$ file ./hexdump.elf

### Make the new ELF executable
$ chmod +x hexdump.elf

### Run the modified program to observe effects of the byte change
$ ./hexdump.elf
