# Useful linux tools

# authbind
If the system doesn't allow a user to bind a certain address they can try:
```bash
# NC
authbind nc -lvnp <port>
# PY
authbind python -m http.server
```