I was solving some reverse engineering CTF's from TryHackMe [https://tryhackme.com/room/reverselfiles](https://tryhackme.com/room/reverselfiles "https://tryhackme.com/room/reverselfiles"), specifically the last one "**crackme8**", to which I wanted to use a different approach than manipulating the execution or even trying to solve the challenge by understanding the logic.

I decided to go through a different route, to manually patch the binary to just print the desired information instead, To do this, we need to gather and check some information before continuing.

## 1 - Identifying the file

> `$ file crackme8`

### Output

> crackme8: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=fef76e38b5ff92ed0d08870ac523f9f3f8925a40, not stripped

There are two important information in the output:

- 32 bit Binary
- File is not stripped(debug symbols are still present)

## 2 - Endianness of the file

Next we need to identify the endianness of the file, to understand how to values will be written in memory

> `$ readelf -h crackme8`

### Output

> ELF Header:  
> Magic: 7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00  
> Class: ELF32  
> Data: 2's complement, little endian  
> Version: 1 (current)  
> OS/ABI: UNIX - System V  
> ABI Version: 0  
> Type: EXEC (Executable file)  
> Machine: Intel 80386  
> Version: 0x1  
> Entry point address: 0x80483a0  
> Start of program headers: 52 (bytes into file)  
> Start of section headers: 4644 (bytes into file)  
> Flags: 0x0  
> Size of this header: 52 (bytes)  
> Size of program headers: 32 (bytes)  
> Number of program headers: 8  
> Size of section headers: 40 (bytes)  
> Number of section headers: 31  
> Section header string table index: 28

## 3 - The target of the patch

Using objdump, let's dump and disassemble only the main function from the binary.  
This step only works because we identified that the binary is non-stripped

> `objdump -C --disassemble="main" --section=".text" crackme8`

|param|desc|
|---|---|
|-C|Decode (demangle) low-level symbol names into user-level names|
|--disassemble|name of the function to be|
|--section|display information for the informed section|

### Output

> crackme8: file format elf32-i386
> 
> Disassembly of section .text:
> 
> 0804849b <main>:  
> 804849b: 8d 4c 24 04 lea 0x4(%esp),%ecx  
> 804849f: 83 e4 f0 and $0xfffffff0,%esp  
> 80484a2: ff 71 fc push -0x4(%ecx)  
> 80484a5: 55 push %ebp  
> 80484a6: 89 e5 mov %esp,%ebp  
> 80484a8: 51 push %ecx  
> 80484a9: 83 ec 04 sub $0x4,%esp  
> 80484ac: 89 c8 mov %ecx,%eax  
> 80484ae: 83 38 02 cmpl $0x2,(%eax)  
> 80484b1: 74 1d je 80484d0 <main+0x35>  
> 80484b3: 8b 40 04 mov 0x4(%eax),%eax  
> 80484b6: 8b 00 mov (%eax),%eax  
> 80484b8: 83 ec 08 sub $0x8,%esp  
> 80484bb: 50 push %eax  
> 80484bc: 68 60 86 04 08 push $0x8048660  
> 80484c1: e8 7a fe ff ff call 8048340 <printf@plt>  
> 80484c6: 83 c4 10 add $0x10,%esp  
> 80484c9: b8 01 00 00 00 mov $0x1,%eax  
> 80484ce: eb 4c jmp 804851c <main+0x81>  
> 80484d0: 8b 40 04 mov 0x4(%eax),%eax  
> 80484d3: 83 c0 04 add $0x4,%eax  
> 80484d6: 8b 00 mov (%eax),%eax  
> 80484d8: 83 ec 0c sub $0xc,%esp  
> 80484db: 50 push %eax  
> 80484dc: e8 9f fe ff ff call 8048380 <atoi@plt>  
> 80484e1: 83 c4 10 add $0x10,%esp  
> 80484e4: 3d 0d f0 fe ca cmp $0xcafef00d,%eax  
> 80484e9: 74 17 je 8048502 <main+0x67>  
> 80484eb: 83 ec 0c sub $0xc,%esp  
> 80484ee: 68 74 86 04 08 push $0x8048674  
> 80484f3: e8 58 fe ff ff call 8048350 <puts@plt>  
> 80484f8: 83 c4 10 add $0x10,%esp  
> 80484fb: b8 01 00 00 00 mov $0x1,%eax  
> 8048500: eb 1a jmp 804851c <main+0x81>  
> 8048502: 83 ec 0c sub $0xc,%esp  
> 8048505: 68 83 86 04 08 push $0x8048683  
> 804850a: e8 41 fe ff ff call 8048350 <puts@plt>  
> 804850f: 83 c4 10 add $0x10,%esp  
> 8048512: e8 0d 00 00 00 call 8048524 <giveFlag>  
> 8048517: b8 00 00 00 00 mov $0x0,%eax  
> 804851c: 8b 4d fc mov -0x4(%ebp),%ecx  
> 804851f: c9 leave  
> 8048520: 8d 61 fc lea -0x4(%ecx),%esp  
> 8048523: c3 ret

The output shows us that there is a function called "**giveflag**", which, considering its name, has the flag we need to solve this level.

> 8048512: e8 0d 00 00 00 call 8048524 <giveFlag>

Knowing this, comes the question, "Where to patch with the least amount of work?"  
In the offset _**0x80484c1**_ there is a call to printf function.

> 80484c1: e8 7a fe ff ff call 8048340 <printf@plt>

This offset uses the opcode **E8**, to a call procedure and means a **Near Call** which uses as an argument the offset displacement relative to next instruction  
**The last part is extremely important; the offset needs to be relative to the next instruction, otherwise you will miss the wanted location**

## 4 - Patching

Now that we know the location, opcode and which function we need to call, let's start thinking about patching the file.  
Considering the endianness of the file is little-endian, the MSB(most significant byte) will be written in the higher memory address, we must have this in mind when writing into the file.  
The offset for the printf function is negative, therefore the use of **"0xFF"**, but the "**giveflag**" function offset is positive, so we will use **"0x00"** instead to indicate a positive number.

The instruction to be altered has the offset **"0x80484c1"** and to calculate the offset of the function we want to call we need to know the address of the next instruction, which for us will be **"0x80484c6"**.

The offset for the "**giveflag**" function is **"0x8048524"** now is simply math :

0x8048524−0x80484c6=0x5E0x8048524 - 0x80484c6 = 0x5E 0x8048524−0x80484c6=0x5E

We must keep the same number of bytes in the binary and the **E8** opcode expects a 32 bit address, therefore we need to fill in the rest of the address.  
Remembering the endianness of the file, **"0x5E"** in a DWord(4 bytes) will be written as **"0x5E000000"**

Using any software to edit the file and change the offset from **0x80484c1: e8 7a fe ff ff call 8048340 <printf@plt>** to **0x80484c1: e8 5e 00 00 00 call 8048524 <giveFlag>**

Now we call the patched binary, and the flag function will be called directly

> $ ./crackme8_patched  
> flag{at_least_this_cafe_wont_leak_your_credit_card_numbers}