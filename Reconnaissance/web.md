# Web enumeration
Commands and procedures to be done when looking through a web page.


# Nmap   
```bash
nmap -p- -v -T5 <ip>
```    
after ports are known    
```bash
nmap -p <ports> -A -v -T5 <ip>
```    
You can use the `-sU` flag to scan for UDP ports **(requires root privileges)**


# Nikto  
```bash
nikto -h http://target
```

# FFUF
   
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
fuf -w <TEXT FILE> -u <HOST> -H "Host: FUZZ.<DOMAIN>"
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
Wfuzz is a tool designed for bruteforcing Web Applications, it can be used for finding resources not linked directories, servlets, scripts, etc.

## Bruteforce internal port (SSRF)
This was written for a HTB machine.
```bash
wfuzz -c -z range,1-65535 --hl=2 http://10.10.10.55:60000/url.php?path=http://localhost:FUZZ
```
The host can also be written like this:
```
http://TARGET/script.php?path=http://localhost:FUZZ
```

# WPScan    
```bash
wpscan --url <url> -e ap,at,dbe,u --plugins-detection aggressive
```