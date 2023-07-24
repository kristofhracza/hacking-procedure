# Windows technologies
Windows tech enumeration

# LDAP
## ldapsearch
```bash
# Anonymous access
ldapsearch -H ldap://<ip>:<port> -b "dc=domain,dc=local" -x

# Naming Contexts
ldapsearch -h <ip> -x -s base namingcontexts

# Get all info
ldapsearch -h <ip> -x -b "dc=domain,dc=local"

# Filter people
ldapsearch -h <ip> -x -b "dc=domain,dc=local" '(objectClass=person)'

# Filter users
ldapsearch -h <ip> -x -b "dc=domain,dc=local" '(objectClass=user)'

# Filter groups
ldapsearch -h <ip> -x -b "dc=domain,dc=local" '(objectClass=group)'
```   
*Make sure to look for users in the Remote Management Users group*  

## windapsearch.py
LDAP enumeration script      
[https://github.com/ropnop/windapsearch](https://github.com/ropnop/windapsearch)
```bash
windapsearch.py --dc-ip <ip> -d domain.local -u "" -U
```

## Useful notes
### ldapsearch passwords in result
If an `ldapsearch` query comes back with users, try checking whether they have any password related options set.    

**Examples**
```
cascadeLegacyPwd: BASE64 STRING
```
​
# SMB
## Enumeration

### smbmap
```bash
# Without creds
smbmap -H <ip>

# With creds
smbmap -H <ip> -u <user> -p <password>
```

### CrackMapExec
```bash
# Enumerate users
crackmapexec smb <ip> -u <user> -p <password> --users

# Perform RID Bruteforce to get users
crackmapexec smb <ip> -u <user> -p <password> --rid-brute

# Enumerate domain groups
crackmapexec smb <ip> -u <user> -p <password> --groups

# Enumerate local users
crackmapexec smb <ip> -u <user> -p <password> --local-users
```

### enum4linux
```bash
enum4linux -U -o <ip>
```

### Null session attack
```bash
smbclient -N -L \\\\<ip>

crackmapexec smb <ip> -u ""
```

### Bruteforcing and password spraying
```bash
crackmapexec smb <ip> -u <username/file> -p <password/file>
```


# Kerberos
## GetNPUsers.py
Impacket's `GetNPUsers.py` will attempt to harvest the non-preauth AS_REP responses for a given list of usernames

### Usage
```bash
GetNPUsers.py domain.local/ -dc-ip <ip> -usersfile <username_file> -format hashcat -outputfile <output>
```

## Kerberoasting
Kerberoasting is a post-exploitation attack technique that attempts to obtain a password hash of an Active Directory account that has a Service Principal Name.

```bash
GetUserSPNs.py -request -dc-ip <ip> domain.local/user -save -outputfile <output_file>
```

### Cracking the ticket
```bash
hashcat -m 13100 --force <hash_file> <password_file>
```

#### References
- [https://www.crowdstrike.com/cybersecurity-101/kerberoasting/](https://www.crowdstrike.com/cybersecurity-101/kerberoasting/)

# RPC
# Enumeration
```bash
# Null authentication
rpcclient -U '' -N <ip>

# With creds
rpcclient -U <username> <ip>
```