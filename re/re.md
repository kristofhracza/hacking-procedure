# Radare2 (R2)
r2 is a complete rewrite of radare. It provides a set of libraries, tools and plugins to ease reverse engineering tasks.     
[https://github.com/radareorg/radare2/blob/master/doc/intro.md](https://github.com/radareorg/radare2/blob/master/doc/intro.md)     
[https://man.archlinux.org/man/radare2.1.en](https://man.archlinux.org/man/radare2.1.en)



# Ghidra
Open-source reverse engineering software developed by NSA      
[Ghidra GitHub](https://github.com/NationalSecurityAgency/ghidra)      
[Ghidra CheatSheet](https://ghidra-sre.org/CheatSheet.html)



# dnSpy
Used for disassembling .NET code
**[Documentation and Release](https://github.com/dnSpy/dnSpy)**


# GDB
GNU Debugger

## peda
**[GitHub](https://github.com/longld/peda)**      
Python Exploit Development Assistance for GDB

## Basic buffer overflow
*[Walkthrough and help](https://samsclass.info/127/proj/p3-lbuf1.htm)*



# UPX
If a binary is packed with `upx` use this to unpack it:
```bash
upx -d <binary>
```



# Python executables
Python files can be packed and unpacked to and from a binary.     

Use the **[extractor](https://github.com/extremecoders-re/pyinstxtractor)** to unpack the binary, then use **[uncompyle6](https://pypi.org/project/uncompyle6/)** to decompile `.pyc` files received from the unpacking process.      

Click **[this](https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux)** for better explanation and examples.



# Angr and claripy
Binary analysis platform

## Use cases
One can write a python script to brute-force a flag in a binary.      
**[This](https://gist.github.com/kristofhracza/cadc99ba82632b5cc6bda3a882c70c50)** script gives an outline of the basic usage of the tool.