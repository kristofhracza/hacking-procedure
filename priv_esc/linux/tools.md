# authbind
If the system doesn't allow a user to bind a certain address they can try:
```bash
# NC
authbind nc -lvnp <port>

# PY
authbind python -m http.server
```


# sssd
`sssd` is an open source client for enterprise identity management.           
It allows for Linux machines to be joined into an Active Directory domain.

`SSSD` maintains a copy of the database at the path `/var/lib/sss/secrets/secrets.ldb`.     
The corresponding key is stored as a hidden file at the path `/var/lib/sss/secrets/.secrets.mkey`. By default, the key is only readable if you have root permissions.

Knowing this informationm one can take a look at those file (if they're present) and extract data from them.    
If data cannot be found in those files, one might try to go back one folder to `/var/lib/sss` where they might find some other files which can potentially reveal some info.            

[**More info**](https://sssd.io/)  



# Chisel
Tunneling tools     
```bash
# SERVER
chisel server --port <port> --reverse

############################# [CLIENTS] #############################

# BASIC SERVER LISTENER
chisel client <server_ip_and_port> R:<listener_port>:<ip>:<forwarded_port>

# SOCKS PROXY
## Note that socks proxy will start a listener on port 1080
chisel client <server_ip_and_port> R:socks
```

## Interaction
One can use `FoxyProxy` for the browser or `proxychains` on the command line to interact with the network.



# deepce
Docker Enumeration, Escalation of Privileges and Container Escapes (DEEPCE)     
**[https://github.com/stealthcopter/deepce](https://github.com/stealthcopter/deepce)**

# Statically compiled binaries
**[https://github.com/andrew-d/static-binaries/](https://github.com/andrew-d/static-binaries/)**