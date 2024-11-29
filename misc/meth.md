# Checklist for myself (mainly)
- Assume that the easiest path is the best
- Enumerate every new finding and connect to previously obtained information

# Terminal tricks
## Shell upgrade to tty
```bash
username@host:~# script /dev/null -c bash

# Press Ctrl+Z
username@host:~# ^Z

# Type: stty raw -echo; fg
localname@localmachine$ stty raw -echo; fg

# Type: reset
reset
reset: unknown terminal type unknown

# Type: screen
Terminal type? screen
                                                                         
username@host:~# 
```

# Common sense
## Sub-folder or file exposed in non-accessible directory
If access is denied to a folder but there's a resource known to be beyond it / inside it, one can try to access the resource.
Either by changing directories or outputting a file.


# Restoring corrupted docx files
1. Copy docx into a zip:   `cp 1.docx 1.zip`
2. Extract non corrupt data to another zip:  `zip -FF 1.zip 2.zip`
3. Copy new zip to new docx:  `cp 2.zip  2.docx`

## Sources
- [https://askubuntu.com/questions/388444/how-can-i-extract-the-data-from-a-corrupted-docx-file](https://askubuntu.com/questions/388444/how-can-i-extract-the-data-from-a-corrupted-docx-file)
- [https://superuser.com/questions/23290/terminal-tool-linux-for-repair-corrupted-zip-files](https://superuser.com/questions/23290/terminal-tool-linux-for-repair-corrupted-zip-files)


# PE Binaries
## x86 or x64
Use `hexdump <binary> -C` to see the first few bytes of the binary.
```
ARHCITECTURE:   x86
50 45 00 00 4c 01 04 00  |........PE..L...|

ARHCITECTURE:   x64
50 45 00 00 64 86 03 00  |........PE..d...|
```
- [https://www.gdatasoftware.com/blog/pebitnesstrick](https://www.gdatasoftware.com/blog/pebitnesstrick)