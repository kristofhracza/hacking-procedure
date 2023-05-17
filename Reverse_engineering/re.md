# Reverse Engineering
Everything that you need for RE

# Radare2 (R2)
Manipulate binaries easily    
You can use R2 to overwrite parts of the assembly code so one part doesn't get called or gets redirected.

## Help
- [https://github.com/radareorg/radare2/blob/master/doc/intro.md](https://github.com/radareorg/radare2/blob/master/doc/intro.md)
- [https://man.archlinux.org/man/radare2.1.en](https://man.archlinux.org/man/radare2.1.en)


# Ghidra
Open-source reverse engineering software developed by NSA

## Help
- [https://github.com/NationalSecurityAgency/ghidra](https://github.com/NationalSecurityAgency/ghidra)
- [https://ghidra-sre.org/CheatSheet.html](https://ghidra-sre.org/CheatSheet.html)



# dnSpy
Used for disassembling .NET code

## Github
[https://github.com/dnSpy/dnSpy](https://github.com/dnSpy/dnSpy)



# GDB
GNU Debugger helps with debugging.    
When looking through a binary one might want to perfrom a buffer overflow attack, for which GDB is perfect.

## peda
Python Exploit Development Assistance for GDB

### Links
- [https://github.com/longld/peda](https://github.com/longld/peda)

## Basic buffer overflow
### References
- **Best help**    [**https://samsclass.info/127/proj/p3-lbuf1.htm**](https://samsclass.info/127/proj/p3-lbuf1.htm)
- [https://www.cyberpunk.rs/buffer-overflow-linux-gdb](https://www.cyberpunk.rs/buffer-overflow-linux-gdb)
- [https://shell-storm.org/shellcode/index.html](https://shell-storm.org/shellcode/index.html)


# UPX
If a binary is packed with `upx` use this to unpack it:
```bash
upx -d <binary>
```


# PyInstaller packed binaries
Python files can be packed and unpacked to and from a binary.    
Use the links below to unpack said binaries.    

After unpacking the binary, use [uncompyle6](https://pypi.org/project/uncompyle6/) to decompile `.pyc` files gotten from the unpacking process.

## The extractor
[https://github.com/extremecoders-re/pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor)

### Help
[https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux](https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux)