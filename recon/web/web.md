# In-Depth enumeration

## Nikto  
Scan sites for known vulnerabilities, misconfigurations and directories.
```bash
nikto -h <host>
```

## FFUF
```bash
# Directory scan
ffuf -u <host>/FUZZ -w <wordlist>
ffuf -u <host>/FUZZ -w <wordlist> -e .txt,.php,.html
# DNS / Sub-domain scan
ffuf -u <ip> -H "Host: FUZZ.host.local" -w <wordlist> -mc all
```

## gobuster
```bash
# Directory scan
gobuster dir -u <IP> -w <wordlist>
gobuster dir -u <IP> -w <wordlist> -x txt,php,html
# DNS / Sub-domain scan
gobuster dns -d <host> -w <wordlist>
# VHOST scanning
gobuster vhost -u <host> -w <wordlist> 
```

## feroxbuster
```bash
# Scan for DIRs
feroxbuster -u <host>
feroxbuster -u <host> -x html,php
```

## Wfuzz
Wfuzz is a tool designed for brute-forcing Web Applications, it can be used for finding resources not linked directories, servlets, scripts, etc.
```sh
# DNS scan
wfuzz -u <ip> -H "Host: FUZZ.domain.local" -w <wordlist>
# Bruteforce internal port (SSRF)
wfuzz -c -z range,1-65535 --hl=2 http://<ip>:<port>/url.php?path=http://localhost:FUZZ
# URL parameter discovery
wfuzz -u https://<link>/<page>/?FUZZ= -w <wordlist> -H "Cookie: PHPSESSID="
```

# WordPress
## WPScan    
WordPress security scanner
```bash
wpscan --url <url> -e ap,at,dbe,u
```

## Ports
```
80      HTTP
443     HTTPS
8000    HTTP Alternative
8080    HTTP Alternative
```