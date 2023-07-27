# Web enumeration
Commands and procedures to be done when looking through a web page.


# Nmap   
```bash
# Scan all ports (TCP)
nmap -p- -v -T5 <ip>

# Scan all ports (UDP)
nmap -p- -v -T5 -sU <ip> 

# Scan ports aggressively (-A) to get better info
nmap -p <ports> -A -v -T5 <ip>
```

# Nikto  
Website vulnerability scanner
```bash
nikto -h http://target
```

# FFUF
Website fuzzing tool
## Directory scan
```bash
ffuf -w /path/to/wordlist -u https://target/FUZZ
```

## Sub-domain scan
```bash
ffuf -w <TEXT FILE> -u https://FUZZ.<DOMAIN>/
```

## Vhost fuzzing
```bash
ffuf -w <TEXT FILE> -u <HOST> -H "Host: FUZZ.<DOMAIN>"
```

# Cewl
Cewl can be used on a webpage, and if one suspects that a password is hidden somewhere in the website.
[https://www.kali.org/tools/cewl/](https://www.kali.org/tools/cewl/)

## Usage
```bash
cewl -w <wordlist> -d 10 -m 1 <url>
```

# On-page / on-site recon
These are all stuff that one should look at when inspecting a web page, to see if there's anything suspicious.
- Links
- Paths
- URL parameters
- Cookies and local storage


# Gobuster
## Scan dir
```sh
gobuster dir -u <IP> -w <wordlist>
```
## Scan for multiple file extensions with the same list
```sh
# -x is to specify the file extension, the ones given are just examples
gobuster dir -u <IP> -w <wordlist> -x txt,php,html
```

# Wfuzz
Wfuzz is a tool designed for brute-forcing Web Applications, it can be used for finding resources not linked directories, servlets, scripts, etc.

## Bruteforce internal port (SSRF)
```bash
wfuzz -c -z range,1-65535 --hl=2 http://10.10.10.55:60000/url.php?path=http://localhost:FUZZ
```

## URL parameter discovery
```bash
wfuzz -u https://<link>/<page>/?FUZZ= -w <wordlist> -H "Cookie: PHPSESSID="
```

# WPScan    
```bash
wpscan --url <url> -e ap,at,dbe,u --plugins-detection aggressive
```