# Assumptions
Things that aren't self explanatory if not encountered before


# Stuff to check for
- **/dev** DIR on web page. If found, use `git-dumper` to get data. 
- Try to access a page with both HTTP and HTTPS. *They might host different content*
- PHP files
    - If one or more file is found during recon, assume that there are more php files on the site and scan for them.    
    (Use a wordlist such as [https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/Common-PHP-Filenames.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/Common-PHP-Filenames.txt))
    - Any other files:      Try the method described [here](./web#scan-for-multiple-file-extensions-with-the-same-list) to expose more files.

- On a webpage scan for all types of files, with DIR names during recon. 
    ```bash 
    gobuster dir -u <IP> -w <wordlist> -x txt,php,html
    ```

# Linux
## .dockerenv
If there is a `.dockerenv` file, it means that the host is most likely a docker container

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

# Sub-folder or file exposed in non-accessible directory
If access is denied to a folder but there's a resource known to be beyond it / inside it, one can try to access the resource.
Either by changing directories or outputting a file.