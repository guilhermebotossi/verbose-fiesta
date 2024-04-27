## Syscalls

### Opcodes X86

> `cat /usr/include/x86_64-linux-gnu/asm/unistd_32.h`

### Opcodes X86_64

> `cat /usr/include/x86_64-linux-gnu/asm/unistd_64.h`

---

## ELF Header

|Offset|Name|Desc|
|---|---|---|
|0x00 - 0xF|EI_IDENT|ELF indentification|
|0x00 - 0x03|EI_MAG0 ... EL_MAG3|First for bytes of every ELF file are the ascii codes for 'E' 'L' 'F'.|
|0x04|EI_CLASS|Basically tells us whether the file is 32 or 64 bit. Standard says 0x1 means 32 bit and 0x2 means 64 bit.|
|0x05|EI_DATA|Defines the endianness of the file 0x01 means little endian and 0x02 means big endian.|
|0x06|EI_VERSION|Shows the version of the ELF file, most should be set to 0x1 for version 1.|

[https://refspecs.linuxfoundation.org/elf/gabi4+/ch4.eheader.html](https://refspecs.linuxfoundation.org/elf/gabi4+/ch4.eheader.html "https://refspecs.linuxfoundation.org/elf/gabi4+/ch4.eheader.html")

---

## Print Header of elf file

> `readelf -h <file>`

## Disassemble function using objdump

### **Only works on non-stripped Binaries**

> `objdump -C --disassemble="<func_name>" --section=".text" <file>`

|param|desc|
|---|---|
|-C|Decode (demangle) low-level symbol names into user-levelnames|
|--disassemble|name of the function to be|
|--section|display information for the informed section|

---