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