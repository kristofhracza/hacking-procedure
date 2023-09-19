# Nmap   
Discover port and services running on the remote host.
```bash
# Scan all ports (TCP)
nmap -p- -v -T5 <ip>

# Scan all ports (UDP)
nmap -p- -v -T5 -sU <ip> 

# Scan ports aggressively (-A) to get better info
nmap -p <ports> -A -v -T5 <ip>
```



# Nikto  
Scan sites for known vulnerabilities, misconfigurations and directories.
```bash
nikto -h http://target
```



# gobuster
Web brute forcer
```bash
# DNS / Sub-domain scan
gobuster dns -d <host> -w <wordlist>

# Directory scan
gobuster dir -u <IP> -w <wordlist>

## Scan for multiple file extensions with the same list (-x)
gobuster dir -u <IP> -w <wordlist> -x txt,php,html

# VHOST scanning
gobuster vhost -u <host> -w <wordlist> 
```



# Wfuzz
Wfuzz is a tool designed for brute-forcing Web Applications, it can be used for finding resources not linked directories, servlets, scripts, etc.

```sh
# Bruteforce internal port (SSRF)
wfuzz -c -z range,1-65535 --hl=2 http://<ip>:<port>/url.php?path=http://localhost:FUZZ

# URL parameter discovery
wfuzz -u https://<link>/<page>/?FUZZ= -w <wordlist> -H "Cookie: PHPSESSID="
```



# Cewl
CeWL (Custom Word List generator) is a ruby app which spiders a given URL, up to a specified depth, and returns a list of words which can then be used for password crackers such as John the Ripper. Optionally, CeWL can follow external links.      

CeWL can also create a list of email addresses found in mailto links. These email addresses can be used as usernames in brute force actions.       
**[Documentation](https://www.kali.org/tools/cewl/)**

```bash
cewl -w <wordlist> -d 10 -m 1 <url>
```



# WPScan    
Scan wordpress sites
```bash
wpscan --url <url> -e ap,at,dbe,u
```