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



# Active Directory Certificate Services (ADCS)
Active Directory Certificate Services (AD CS) is a Windows Server role for issuing and managing public key infrastructure (PKI) certificates used in secure communication and authentication protocols.     
- [https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/active-directory-certificate-services-overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/active-directory-certificate-services-overview)

## Enumeration
Here, one should look for services that haven't been discovered before.
```sh
crackmapexec ldap <ip> -u <username> -p <password> -M adcs
```