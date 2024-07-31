# Wordlists
- [rockyou.txt](https://github.com/zacheller/rockyou)
- [Sub-domains (1)](https://github.com/n0kovo/n0kovo_subdomains)
- [Sub-domains (2)](https://github.com/rbsec/dnscan/tree/master)
- [Directories (1)](https://github.com/daviddias/node-dirbuster/tree/master/lists)
- [Directories (2)](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/raft-medium-directories-lowercase.txt) *used by Feroxbuster*
- [SNMP community strings](https://github.com/fuzzdb-project/fuzzdb/blob/master/wordlists-misc/wordlist-common-snmp-community-strings.txt)

# Useful websites
- [Reverse shell generator](https://www.revshells.com/)
- [Hashcat hash examples](https://hashcat.net/wiki/doku.php?id=example_hashes)
- [Crackstation](https://crackstation.net/)
- [WasmFiddle - C to Web Assembly](https://wasdk.github.io/WasmFiddle/)     

# Browser extensions
- [Requestly](https://chromewebstore.google.com/detail/requestly-open-source-htt/mdnleldcmiljblolnjhpnblkcekpdkpa?pli=1)
- [FoxyProxy](https://chromewebstore.google.com/detail/foxyproxy-basic/dookpfaalaaappcdneeahomimbllocnb)

# Binaries
- [Statically compiled binaries](https://github.com/andrew-d/static-binaries/)
- [SharpCollection - Pre-compiled binaries (windows)](https://github.com/Flangvik/SharpCollection/tree/master/NetFramework_4.5_x64)

# Docker
- [Docker Enumeration, Escalation of Privileges and Container Escapes (DEEPCE)](https://github.com/stealthcopter/deepce)

# Tunneling and Listeners
## Chisel
[Chisel](https://github.com/jpillora/chisel) is a fast TCP/UDP tunnel, transported over HTTP, secured via SSH. Single executable including both client and server.    
```bash
############################# [SERVER] #############################
chisel server --port <port> --reverse

############################# [CLIENTS] #############################

# BASIC client
chisel client <server_ip_and_port> R:<listener_port>:<ip>:<forwarded_port>

# SOCKS PROXY
## Note that socks proxy will start a listener on port 1080
chisel client <server_ip_and_port> R:socks
```

### Interaction
One can use `FoxyProxy` for the browser or `proxychains` on the command line to interact with the network.

## authbind
authbind allows a program which does not or should not run as root to bind to low-numbered ports in a controlled way.
```bash
# Netcat
authbind nc -lvnp <port>

# Python
authbind python -m http.server
```

# Active Directory
## BloodHound
BloodHound is used to visualise AD environments and discover attack paths.        
- [Documentation](https://bloodhound.readthedocs.io/en/latest/index.html)

### Ingestors
#### SharpHound
Data collector for BloodHound     
- [Documentation](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)         
- [Releases](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors)

#### bloodhound.py
Python based data collection tool for BloodHound        
This will run against the domain, so can one run it from a remote machine.
```bash
bloodhound-python -u <username> -p <password> -d <domain> -c All -ns <nameserver>                
```
- [Documentation](https://github.com/fox-it/BloodHound.py)

## sssd
`sssd` is an open source client for enterprise identity management.           
It allows for Linux machines to be joined into an Active Directory domain.

`SSSD` maintains a copy of the database at the path `/var/lib/sss/secrets/secrets.ldb`.     
The corresponding key is stored as a hidden file at the path `/var/lib/sss/secrets/.secrets.mkey`. By default, the key is only readable if you have root permissions.

Knowing this information one can take a look at those file (if they're present) and extract data from them.    
If data cannot be found in those files, one might try to go back one folder to `/var/lib/sss` where they might find some other files which can potentially reveal some info.            

[**More info**](https://sssd.io/)  

## Miscellaneous
- [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)
- [Powermad](https://github.com/Kevin-Robertson/Powermad)
- [Rubeus Docs](https://github.com/GhostPack/Rubeus)
- [Rubeus Release](https://github.com/Flangvik/SharpCollection/blob/master/NetFramework_4.5_x64/Rubeus.exe)
- [SharpCollection - Pre-compiled binaries (windows)](https://github.com/Flangvik/SharpCollection/tree/master/NetFramework_4.5_x64)

# Reverse Engineering
## Radare2 (R2)
r2 is a complete rewrite of radare. It provides a set of libraries, tools and plugins to ease reverse engineering tasks.     
- [Introduction](https://github.com/radareorg/radare2/blob/master/doc/intro.md)     
- [Manual](https://man.archlinux.org/man/radare2.1.en)

## Ghidra
Open-source reverse engineering software developed by NSA      
- [Ghidra GitHub](https://github.com/NationalSecurityAgency/ghidra)      
- [Ghidra CheatSheet](https://ghidra-sre.org/CheatSheet.html)

## GDB
GNU Debugger

### peda
[GitHub](https://github.com/longld/peda)          
Python Exploit Development Assistance for GDB

### Basic buffer overflow
[Walkthrough and help](https://samsclass.info/127/proj/p3-lbuf1.htm)

## dnSpy
[https://github.com/dnSpy/dnSpy](https://github.com/dnSpy/dnSpy)                
Used for disassembling .NET code

## Python executables
Python files can be packed and unpacked to and from a binary.       

Use the **[extractor](https://github.com/extremecoders-re/pyinstxtractor)** to unpack the binary, then use **[uncompyle6](https://pypi.org/project/uncompyle6/)** to decompile `.pyc` files received from the unpacking process.      

Click **[this](https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux)** for better explanation and examples.

## Miscallenous
- `upx` for packing and unpacking binaries.
- [Angr / Claripy - Abstracted constraint-solving wrapper](https://github.com/angr/claripy?tab=readme-ov-file)
    - Can be used to brute-force CTF flags in a binary.