# SNMP
Simple Network Management Protocol      

Install with `apt install snmp-mibs-downloader`

```bash
# Scanning
snmpwalk -Os -c public <ip>
```


# WebDav
## Scanning
1. `davtest`
2. In metasploit: `search webdav`
3. If vulnerable use: ​[https://github.com/Re4son/Churrasco/](https://github.com/Re4son/Churrasco/)


# Apache Subversion (SVN)
Software versioning and revision control system
```sh
# Get files on the server
svn co <url>
```


# QUIC protocol
[https://www.debugbear.com/blog/http3-quic-protocol-guide](https://www.debugbear.com/blog/http3-quic-protocol-guide)     
[https://en.wikipedia.org/wiki/QUIC](https://en.wikipedia.org/wiki/QUIC)

## Access pages with Curl
```bash
curl --http3 https://site.com
```

## Build Curl from source
This is if your version of `curl` doesn't support QUIC.
[https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version](https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version)