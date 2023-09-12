# username-anarchy
Tool for generating usernames
[https://github.com/urbanadventurer/username-anarchy](https://github.com/urbanadventurer/username-anarchy)

# authbind
If the system doesn't allow a user to bind a certain address they can try:
```bash
# NC
authbind nc -lvnp <port>
# PY
authbind python -m http.server
```

# Chisel
HTTP tunnel     
```bash
# SERVER
chisel server --port <port> --reverse

# CLIENT
chisel client <server_ip_and_port> R:<local_port>:<ip>:<server_port>
```

# deepce
Docker Enumeration, Escalation of Privileges and Container Escapes (DEEPCE)     
[https://github.com/stealthcopter/deepce](https://github.com/stealthcopter/deepce)

# Statically compiled binaries
[https://github.com/andrew-d/static-binaries/](https://github.com/andrew-d/static-binaries/)