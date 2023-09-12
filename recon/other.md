# SNMP
Simple Network Management Protocol

## Installation
`apt install snmp-mibs-downloader`

## Scanning
- `snmpwalk -Os -c public -v 1 <ip>`
- `snmpwalk -Os -c public <ip>`


# WebDav
## Scanning tools / methods
- `davtest`
- In metasploit: `search webdav`
- If vulnerable use: ​[https://github.com/Re4son/Churrasco/](https://github.com/Re4son/Churrasco/)


# Apache Subversion (SVN)
Software versioning and revision control system
## To see files on the server
`svn co <url>`


# QUIC protocol

## Info
- [https://www.debugbear.com/blog/http3-quic-protocol-guide](https://www.debugbear.com/blog/http3-quic-protocol-guide)
- [https://en.wikipedia.org/wiki/QUIC](https://en.wikipedia.org/wiki/QUIC)

## Access pages with Curl
```bash
curl --http3 https://site.com
```

### Build Curl from source
This is if your version of `curl` doesn't support QUIC.
[https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version](https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version)