# File with non-compatible characters
`hexdump` will show all the characters
```bash
cat <file> | hexdump -C
```

# Password protected zip files
Use `fcrackzip` to crack the password

```bash
fcrackzip <zip_file> -u -v  -D -p <wordlist>
```

# Jamovi OMV files
OMV files are used for Jamovi to display data.     
These files are basically zip archives so:      

- Run `unzip -l <file>`     
**If `xdata.json` is present**     
Run `unzip <file>` and open `xdata.json` and read data.

## Reference
- [https://docs.jamovi.org/_pages/info_file-format.html#xdata-json](https://docs.jamovi.org/_pages/info_file-format.html#xdata-json)

# Analyse office files
Modern *Office* documents are just zip archives with XML files so, just unzip it and look for data within the XML files.

## Unzip
```bash
unzip <file>
```

## oletools
```bash
# Download tools
sudo pip3 install -U oletools

# Extract macros
olevba -c <file>
```

### References
- [https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/specific-software-file-type-tricks/office-file-analysis](https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/specific-software-file-type-tricks/office-file-analysis)

# PFX files
Password protected file certificate commonly used for code signing your application. It derives from the PKCS 12 archive file format certificate, and it stores multiple cryptographic objects within a single file     

## Extract private key and certificate
```bash
# PRIVATE KEY
openssl pkcs12 -in <pfx_file> -nocerts -out private.key

# CERTIFICATE
openssl pkcs12 -in <pfx_file> -clcerts -nokeys -out certificate.crt
```

### Password protection
`pfx2john` can turn these files into a hash which can be brute-forced to extract password.
```bash
# Make hash file
pfx2john.py <file> > <hashfile>

# Dictionary attack
john -w=/usr/share/wordlists/rockyou.txt <hashfile>

# Brute force
john  <hashfile>

# One liners
pfx2john <file>  |john /dev/stdin
pfx2john <file>  |john -w=/usr/share/wordlists/rockyou.txt /dev/stdin
```

## Login with evil-winrm
```bash
evil-winrm -i <ip> -S -k <private_key> -c <certificate>
```