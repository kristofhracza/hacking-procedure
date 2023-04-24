# Web enumeration
Commands and procedures to be done when looking through a web page.


## Nmap
Network mapper

*Usage*    
`nmap -p- -v <ip>`    
after ports are known    
`nmap -p <ports> -A -v <ip>`    
You can use the `-sU` flag to scan for UDP ports **(requires root privileges)**


## Nikto
Nikto is a vulnerability scanner for web servers    

*Usage*    
`nikto -h http://target`


## FFUF
Fuzz Faster U Fool is a tool to scan a web server for directories.

*Usage*    
`ffuf -w /path/to/wordlist -u https://target/FUZZ`


## On-page / on-site recon
These are all stuff that one should look at when inspecting a web page, to see if there's anything suspicious.
- Links
- Paths
- URL parameters
- Cookies and local storage


## Wordpress
If a site is using wordpress, scan the site with WPScan
*Usage*    
`wpscan --url <url> -e ap,at,dbe,u --plugins-detection aggressive`