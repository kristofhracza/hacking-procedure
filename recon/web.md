# Scanning
## Nmap   
Discover port and services running on the remote host.
```bash
# Scan all ports (TCP)
nmap -p- -v -T5 <ip>

# Scan all ports (UDP)
nmap -p- -v -T5 -sU <ip> 

# Scan ports aggressively (-A) to get better info
nmap -p <ports> -A -v -T5 <ip>
```


## Nikto  
Scan sites for known vulnerabilities, misconfigurations and directories.
```bash
nikto -h <host>
```



# Web URI brute-force
## FFUF
```bash
# Directory scan
ffuf -u <host>/FUZZ -w <wordlist>

## Scan for multiple file extensions with the same list (-x)
ffuf -u <host>/FUZZ -w <wordlist> -e .txt,.php,.html

# DNS / Sub-domain scan
## host format is: http://FUZZ.domain.com
ffuf -u <host> -w <wordlist>
```


## gobuster
```bash
# Directory scan
gobuster dir -u <IP> -w <wordlist>

## Scan for multiple file extensions with the same list (-x)
gobuster dir -u <IP> -w <wordlist> -x txt,php,html

# DNS / Sub-domain scan
gobuster dns -d <host> -w <wordlist>

# VHOST scanning
gobuster vhost -u <host> -w <wordlist> 
```


## Wfuzz
Wfuzz is a tool designed for brute-forcing Web Applications, it can be used for finding resources not linked directories, servlets, scripts, etc.

```sh
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