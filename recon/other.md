# SNMP
Simple Network Management Protocol is a protocol used to monitor different devices in the network (like routers, switches, printers, IoTs...).

## Enumeration
```bash
snmpwalk -Os -c public <ip>

snmpcheck <ip>

msf> use auxiliary/scanner/snmp/snmp_enum
```

## Brute-force Community Strings
```bash
hydra -P <wordlist> <host> snmp
onesixtyone -c <wordlist> <ip>
msf> use auxiliary/scanner/snmp/snmp_login
```

## Resources
- [Wordlist for community strings](https://github.com/fuzzdb-project/fuzzdb/blob/master/wordlists-misc/wordlist-common-snmp-community-strings.txt)


# WebDav
## Scanning
1. `davtest`
2. In metasploit: `search webdav`
3. If vulnerable use: **[Churrasco](https://github.com/Re4son/Churrasco/)**


# Apache Subversion (SVN)
Software versioning and revision control system
```sh
# Get files on the server
svn co <url>
```

# NFS
NFS allows a system to share directories and files with others over a network. 
## Enumeration
```bash
# List NFS shares
/usr/sbin/showmount -e <IP>
```
## Mount Shares
```bash
# Make directory for the mount point
mkdir /tmp/mount

# Mount the share
sudo mount -t nfs <IP>:<share> /tmp/mount/ -nolock
```

# QUIC Protocol
*QUIC* is a general-purpose transport layer network protocol     
- [Explanation and guide](https://www.debugbear.com/blog/http3-quic-protocol-guide)
- [Wikipedia](https://en.wikipedia.org/wiki/QUIC)

## Access Pages with Curl
```bash
curl --http3 https://site.com
```

## Build Curl From Source
Refer to **[this](https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version)** if your version of `curl` doesn't support QUIC.