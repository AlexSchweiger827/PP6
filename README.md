# PP6

## Goal

In this exercise you will:

* Learn how to produce text output to the terminal in four different environments:

  1. **Bash** shell scripting
  2. **GNU Assembler** (GAS) using system calls
  3. **C** via the standard library
  4. **Python 3** using built‑in printing functions

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. When time is up, stop immediately and record exactly where you paused.

---

> **⚠️ WARNING:** The **workflow** steps have been **updated** for PP6. Please review the new workflow below carefully before proceeding.

## Workflow

1. **Fork** this repository on GitHub
2. **Clone** your fork to your local machine
3. Create a **solutions** directory at the repository root: `mkdir solutions`
4. For each task, add your solution file into `./solutions/` (e.g., `[solutions/print.sh](./solutions/print.sh)`)
5. **Commit** your changes locally, then **push** to GitHub
6. **Submit** your GitHub repository link for review

---

## Prerequisites

* Several starter repos are available here:
  [https://github.com/orgs/STEMgraph/repositories?q=SSH%3A](https://github.com/orgs/STEMgraph/repositories?q=SSH%3A)

---

> **Note:** Place all your solution files under the `solutions/` directory in your cloned repo.

## Tasks

### Task 1: Bash Printing & Shell Prompt

**Objective:** Use both `echo` and `printf` to format and display text and variables, and customize your shell prompt and login/logout messages.

1. In `./solutions/`, create a file named `print.sh` based on the template below.
2. At the top of `print.sh`, add the shebang:

   ```bash
   #!/usr/bin/env bash
   ```
3. Implement at least three functions:

   * `print_greeting`: prints `Hello from Bash!` using `echo`.
   * `print_vars`: declares two variables (e.g., `name="Bash"`, `version=5.1") and prints them with `printf\` using format specifiers.
   * `print_escape`: demonstrates escape sequences: newline (`\n`), tab (`\t`), and ANSI color codes (e.g., `\e[32m` for green).
4. Make the script executable and verify it runs:

   ```bash
   chmod +x solutions/print.sh
   ./solutions/print.sh
   ```
5. Modify your **`~/.bashrc`** on your local machine to:

   * Change the `PS1` prompt to include your username and current directory (e.g., `export PS1="[\u@\h \W]\$ "`).
   * Display a **welcome** message on login (e.g., `echo "Welcome, $(whoami)!"`).
   * Display a **goodbye** message on logout (add a `trap` for `EXIT` to echo `Goodbye!`).
6. Reload your shell (`source ~/.bashrc`) and demonstrate the prompt, greeting, and exit message.
   
![Greeting and exit message](https://github.com/AlexSchweiger827/PP6/blob/master/bashrc.PNG?raw=true)

**Template for `solutions/print.sh`**

```bash
#!/usr/bin/env bash

print_greeting() {
    # TODO: echo "Hello from Bash!"
}

print_vars() {
    local name="Bash"
    local version=5.1
    # TODO: printf "Using %s version %.1f\n" "$name" "$version"
}

print_escape() {
    # TODO: printf "This is a newline:\nThis is a tab:\tDone!\n"
    # TODO: printf "\e[32mGreen text\e[0m and normal text\n"
}

# Call your functions:
print_greeting
print_vars
print_escape
```

**Solution Reference**
Place your completed `print.sh` in `solutions/` and commit. Then link it here:

```
[print.sh](https://github.com/AlexSchweiger827/PP6-solutions/blob/main/print.sh)
```

#### Reflection Questions

1. **What is the difference between `printf` and `echo` in Bash?**
```bash
`printf` offers many formatting options which makes the controlling of the output precisely.
You can for example set the width, precision and the alignement of the output.
printf needs a format string, followed by a list of arguments to fill into the format string.
e.g.

printf "format string" arg1, arg2 ....

`echo`  does not need a format string.
e.g.

echo [option] arg1, arg2 ...

`echo` adds a new new line to the output by default, while `printf` need "\n" in the format string to create a new line.
`echo` is good for simple text display and `printf` is good for precise control of output formats. 
```  
2. **What is the role of `~/.bashrc` in your shell environment?**
```bash
`~/.bashrc` is a configuration file which starts by opening the terminal.It initializes an interactive shell session.
In the `~/.bashrc` you costomize your shell environment by changing or adding commands in that file.
For example you can change the colour of the prompt, add messages by starting the shell or add scripts that makes the work with the shell easier.
```   
3. **Explain the difference between sourcing (`source ~/.bashrc`) and executing (`./print.sh`).**

 ```bash

<h1>Sourcing</h1> 
The commands will run in the current shell process and changes to the environment has an effect in the current shell.
to source a file, the file does not need to be excutable. 

<h2>Executing</h2> 
With `executing` the script run the commands in a new shell process. It changes the environment, takes effect of the new shell and
is terminated once the script is done. 
```  

---

### Task 2: GAS Printing (32‑bit Linux)

**Objective:** Use the Linux `sys_write` and `sys_exit` system calls in GAS to write text, while ensuring your repo only contains source files.

1. At the repository root, create a file named `.gitignore` that ignores:

   * Object files (`*.o`)
   * Binaries/executables (e.g., `print`, `print_c`)
   * Any editor or OS-specific files
     Commit this `.gitignore` file.
     **Explain:** Why should compiled artifacts and binaries not be committed to a Git repository?
```bash
Explanation:
1.) Storing compiled artifacts (o.files) and binaries (executables) leads to redundanat data storage, which bloats the repository size unnecessarily.

2.) Binaries are often specific to operating system in the CPU architecture. By committing them you need to maintain multiple versions for different plattforms.

3.) It is difficult to review changes in the binary files, which makes it harder to spot harmful code changes. The source code, on the other hand, is transparent and can easily be reviewed.

4.) Binary files causing merge conflicts. Git can only merge text based files. When binaries change, Git sees them a different files, which can cause a lost of changes.


``` 
2. In `./solutions/`, create a file named `print.s` using the template below.
3. Define a message in the `.data` section (e.g., `msg: .ascii "Hello from GAS!\n"`, `len = . - msg`).
4. In the `.text` section’s `_start` symbol, invoke `sys_write` (syscall 4) and then `sys_exit` (syscall 1) via `int $0x80`.
5. Assemble and link in 32‑bit mode:

   ```bash
   as --32 -o print.o solutions/print.s
   ld -m elf_i386 -o print print.o
   ```
6. Run `./print` and verify it outputs your message.

**Template for `solutions/print.s`**

```asm
    .section .data
msg:    .ascii "Hello from GAS!\n"
len = . - msg

    .section .text
    .global _start
_start:
    movl $4, %eax        # sys_write
    movl $1, %ebx        # stdout
    movl $msg, %ecx
    movl $len, %edx
    int $0x80

    movl $1, %eax        # sys_exit
    movl $0, %ebx        # status 0
    int $0x80
```

**Solution Reference**

```
[print.s](https://github.com/AlexSchweiger827/PP6-solutions/blob/main/print.s)
```

#### Reflection Questions

1. **What is a file descriptor and how does the OS use it?**
 ```bash
A file descriptor is a process-unique identifier for a file or other input/output resources. When you open a file, the operating system creates an entry to represent that file and store the information about that file.
It is a non negative integer, which shows the numbers of entries in your operating system.
The integer value of the file descriptor has diffrent functions.
-Integer value 0 = Standard input
-Integer value 1 = Standard output
-Integer value 2 = Standard error

When a programm request to open a file (e.g open(), create(), socket(), pipe()) the kernel returns a file descriptor to the process.
The table contains information of the opened files like The current position within the file for reading/writing (File Offset), How the file was opened (read-only, write- only, etc.) (Access Mode), How many file descriptors currently point this open file table entry (Reference count), a pointer to the inode of the file, which contains metadata about the actual file on the disk (Pointer to Inode Table Entry).
The kernel maintains a table of all open file descriptors, which are in use.

1.) It makes a system call (e.g read(), write()) and passes the file descriptor as an argument
2.) The kernel receives the file descriptor and looks it up in the table
3.) The open file table entry points to the actual inode of the file on disk
4.) The kernel then performs the requested operation (read bytes, write bytes) based on the information about the resources and the process permissions. 
```
2. **How can you obtain or duplicate a file descriptor for another resource (e.g., a file or socket)?**
The system calls for duplicating another resources are dup(), dup2() and fcntl().
The duplicated resource and the duplication share locks, file position pointers and flags.
So if the duplicated resource is modified the duplication also changes.

```bash
example script of a duplication: 
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <errno.h>

int main()
{
        int original_fd, new_fd;
//Declaration of original_fd
// O_RDWR= The file is readable and writable
// O_CREAT= creates the file if the file is not available
        original_fd=open("original_fd.txt", O_RDWR| O_CREAT );
        new_fd=dup(original_fd);
        printf("original fd: %d \n",original_fd);

        printf("new fd: %d \n",new_fd);
//Write in the original file descriptor
        write(original_fd,"Written to old fd\n",18);
//Write in the new file descriptor
        write(new_fd,"Written to new fd\n",18);
        return 0;
}
```
The output of the file descriptor integer:
original fd: 3
new fd: 4
The file descripto for "original fd" is 3 and for "new fd" is 4.

The output of the textfile:
Written to old fd
Written to new fd

4. **What might happen if you use an invalid file descriptor in a syscall?**
 1. The system call returns -1 (Error indicator)
 2. The Errno is set to EBDAF ("Bad file descriptor) (in C/C++)
 3. The programm does not crash, because the kernel handles the error
 4. If the operation succeeded without an error, the invalid FD can take some place, which leads to resource 
    leaks. This could slow down the system or make it instable. It can also crash other processes.

---

### Task 3: C Printing

**Objective:** Use `printf` and `puts` from `<stdio.h>` to display formatted data.

1. In `./solutions/`, create `print.c` based on the template below.
2. Implement `main()` to print a greeting, an integer, a float, and a string via `printf`, then use `puts`.
3. Compile and run:

   ```bash
   gcc -std=c11 -Wall -o print_c solutions/print.c
   ./print_c
   ```

**Template for `solutions/print.c`**
<
```c
#include <stdio.h>

int main(void) {
    printf("Hello from C!\n");
    printf("Integer: %d, Float: %.2f, String: %s\n", 42, 3.14, "test");
    puts("This is puts output");
    return 0;
}
```

**Solution Reference**

```
[print.c](https://github.com/AlexSchweiger827/PP6-solutions/blob/main/print.c)
```

#### Reflection Questions

1. **Use `objdump -d` on `print_c` to find the assembly instructions corresponding to your `printf` calls.**
```bash
   0000000000001070 <printf@plt>:
    1070:       f3 0f 1e fa             endbr64
    1074:       ff 25 56 2f 00 00       jmp    *0x2f56(%rip)        # 3fd0 <printf@GLIBC_2.2.5>
    107a:       66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)
```

2. **Why is the syntax written differently from GAS assembly? Compare NASM vs. GAS notation.**
   ```bash
  NASM (Netwide Assembler) follows the Intel syntax. It is the syntax documented by Intel for the x86 processors.
 The NASM syntax is: 
 destination, source (e.g. mov eax, ebx)

GAS (GNU Assembler) has the AT&T syntax which can be used general.GAS is for  GAS does not have any support for defining x86 segments. GAS is limited to creating simple single segment 16-bit binary images. Modern GAS do support a directive called .intell_syntax, which allows the use of Intel syntax with GAS.

The GAS syntax is: 
source, destination (e.g. movl %ebx, %eax)

The difference between those both syntaxes has a historical reason. GAS syntax originated for older Unix systems, while NASM is for Intel's own documentation and assembler.
   ```
3. **How could you use `fprintf` to write output both to `stdout` and to a file instead? Provide example code.**
#include <stdio.h>
#include <stdlib.h>


int main() {
    const char*text="text.txt";
    fprintf(stdout, "This is written to stdout and '%s'.\n",text);
    freopen("text.txt", "a+", stdout);
    return 0;
}

The stdout does not transfer to the text.txt file.
---

### Task 4: Python 3 Printing

**Objective:** Use Python’s `print()` function with various parameters and f‑strings.

1. In `./solutions/`, create `print.py` using the template below.
2. Implement `main()` to print a greeting, multiple values with custom `sep`/`end`, and an f‑string expression.
3. Make it executable and run:

   ```bash
   chmod +x solutions/print.py
   ./solutions/print.py
   ```

**Template for `solutions/print.py`**

```python
#!/usr/bin/env python3

def main():
    print("Hello from Python3!")
    print("A", "B", "C", sep="-", end=".\n")
    value = 2 + 2
    print(f"Two plus two equals {value}")

if __name__ == "__main__":
    main()
```

**Solution Reference**

```
[print.py](https://github.com/YOUR_USERNAME/REPO_NAME/blob/main/solutions/print.py)
```

#### Reflection Questions

1. **Is Python’s print behavior closer to Bash, Assembly, or C? Explain.**
2. **Can you inspect a Python script’s binary with `objdump`? Why or why not?**

---

**Remember:** Stop working after **90 minutes** and document where you stopped.

I finished task 1-3 in 95minutes.
