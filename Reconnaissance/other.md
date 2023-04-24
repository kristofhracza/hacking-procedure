# Other technologies
Recon tactics for technologies that didn't fit any other category


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