# File with non-compatible characters
`hexdump` will show all the characters
```bash
cat <file> | hexdump -C
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

# PFX and P12 files with pfx2john
`pfx2john` can turn these files into a hash which can be bruteforced to extract password.
```bash
# Make hash file
pfx2john.py <file> > file.hash

# Brute force
john -w=/usr/share/wordlists/rockyou.txt <hashfile>
```