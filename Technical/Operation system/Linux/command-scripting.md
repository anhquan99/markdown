# Command Scripting
## Shells
- `/bin/sh`  
- `/bin/bash`  
- `/bin/tcsh`  
- `/bin/csh`
- `/bin/ksh`
- `/bin/zsh`
## Shell scripts
- A shell is simply a command line interpreter which provides the user interface for terminal windows. A command shell can also be used to run scripts, even in non-interactive sessions without a terminal window, as if the commands were being directly typed in.
```bash
#!/bin/bash
# the full path of the command interpreter
```
## Return values
- All shell scripts generate a return value upon finishing execution.
- Return values permit a process to monitor the exit state of another process, often parent-child relationship.
- By convention, success is returned as 0, and failure is returned as any non 0 value.
## Syntax
| Character | Description                                                                                                                                               |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `#`       | Used to add a comment, except when used as `\#`, or as `#!` when starting a script                                                                        |
| `\`       | Used at the end of a line to indicate continuation on to the next line, or to indicate that the next character is to be interpreted literally, as in `\$` |
| `;`       | Used to interpret what follows as a new command to be executed after completion of the current command                                                    |
| `$`       | Indicates what follows is an environment variable                                                                                                         |
| `>`       | Redirect output                                                                                                                                           |
| `>>`      | Append output                                                                                                                                             |
| `<`       | Redirect input                                                                                                                                            |
| &#124;    | Used to pipe the result into the next command                                                                                                             |
```bash
make ; make install ; make clean # all excecute even ones fail
make && make install && make clean # abort subsequent commands when an earlier one fails
```
- If the first command fails, the second one will never be executed, use `||`.
## Script parameters
| Parameter            | Meaning                       |
| -------------------- | ----------------------------- |
| `$0`               | Script name                   |
| `$1`               | First parameter               |
| `$2`, `$3`, etc. | Second, third parameter, etc. |
| `$`*               | All parameters                |
| `$#`               | Number of arguments           |
## Command substitution
- Use `$(<command>)`.
## Export variables
- By default, the variables created within a script are available only to the subsequent steps of that script. Any child processes (sub-shells) do not have automatic access to the values of these variables. To make them available to child processes, they must be promoted to environment variables using the export statement.
- The child process is allowed to modify exported variables, but the change in this case will not propagate back to the parent shell since exported variables are not shared, but only copied.
## Arithmetic expressions
- Using `expr`.
```bash
expr 8+8
```
- Using `$((...))`.
```bash
echo $((x+1))
```
- Using `let`.
## String
- Use single square bracket `[]` or doubled square brackets `[[]]` to do comparison.
```bash
let x=(1+2)
```
```ad-note
The modern doubled square brackets `[[]]` helps avoid some errors, such as those that can arise when doing a comparison with empty strings and environment variables.
```
### Parts of a string
- To extract the first `n` characters of a string, use `${string:0:n}`.
- To extract all characters in string after a dot `.`, use `${string#*.}`.
## Loop in bash
- `for`
```bash
for i in list
do
execute
done
```
- `while`
```bash
while condition is true
do
	execute
done
```
- `until`
```bash
until condition is false
do
	execute
done
```
## Debugging script
- In command line, use `bash -x <filename>`.
- In bash file, use:
	- `set -x` to turn on debugging.
	- `set +x` to turn off debugging.
## Redirect errors to file and screen
| File Stream | Description                                                                                        | File Descriptor |
| ----------- | -------------------------------------------------------------------------------------------------- | --------------- |
| `stdin`   | Standard Input, by default the keyboard/terminal for programs run from the command line            | 0               |
| `stdout`  | Standard output, by default the screen for programs run from the command line                      | 1               |
| `stderr`  | Standard error, where output error messages are shown or saved; by default, the same as `stdout` | 2               |
- Usage `<bash-file> 2> <outputfile>`.
```bash
./test.sh 2> error.log # output the error to error.log file
grep -r 'test' dir 1> result 2>&1 # error message mix with standard message 
```
## Create temporary files and directories
- Use `mktemp` to create temporary files or directories (with `-d` option).
- Usually for prevent hacker know which common files or directories exist.
```bash
mktemp test.XXXXX # create a temporary file
mktemp -d testd.XXXXX # create a temporary directory
```
## `/dev/null`
- All output is discarded when written to this file.
- This pseudofile is also called the bit bucket or black hole.
## How the Kernel Generates Random Numbers
- Some servers have hardware random number generators that take as input different types of noise signals, such as thermal noise and photoelectric effect. A transducer converts this noise into an electric signal, which is again converted into a digital number by an A-D converter. This number is considered random. However, most common computers do not contain such specialized hardware and, instead, rely on events created during booting to create the raw data needed.
- Regardless of which of these two sources is used, the system maintains a so-called **entropy** **pool** of these digital numbers/random bits. Random numbers are created from this entropy pool.
- The Linux kernel offers the `/dev/random` and `/dev/urandom` device nodes, which draw on the entropy pool to provide random numbers which are drawn from the estimated number of bits of noise in the entropy pool.
- `/dev/random` is used where very high-quality randomness is required, such as a one-time pad or key generation, but it is relatively slow to provide values. `/dev/urandom` is faster and suitable (good enough) for most cryptographic purposes.
- Furthermore, when the entropy pool is empty, `/dev/random` is blocked and does not generate any number until additional environmental noise (network traffic, mouse movement, etc.) is gathered, whereas `/dev/urandom` reuses the internal pool to produce more pseudo-random bits.