hello there
I write down things that I learnt in this module (linux luminarium) here

## Tip 1
Linux explicitly avoids automatically looking in the current directory when you provide a "naked" path.

This is actually a safety measure: if Linux searched the current directory for programs every time you entered a naked path, you could accidentally execute programs in your current directory that happened to have the same names as core system utilities!

## diff command

```sh
diff file1 file2
```

example

```
diff file1 file2
2c2
< world
---
> universe
```

The output tells us that line 2 changed (2c2), with world in the first file (<) being replaced by universe in the second file (>).

example 2

```
diff old new
1a2
> college
```

This tells us that after line 1 in the first file, the second file has an additional line (1a2 means "after line 1 of file1, add line 2 of file2").

## man command

man has 8 sections. for example "man 7 something" get section 7 for something.

```sh
man -f command
```

this command gives a brief introduction of the given command.
equivalent of "whatis" command.

```sh
man -k regex/string
```

look for the given text in all the man pages and give out the pages that contain that string

---

## Globbing

when you enter a command at command line it performs "Expansion" before executing the command. one the expansions is "Globbing".

Globbing lets you reference files without typing them all.

\* -> match anything (except . and /)
? -> match a single character
\[\] -> match a single character from this set ( \[abcd\] )

---

## Redirection

you know already about ( >, >>, <, 2>, 3> , ...)
 > : redirect FD=1 (standard output) to a file
 >> : append mode of >
 < : redirect FD=0 (standard input) from a file to process
 NUM> : redirecd FD=NUM (for example 2> redirect standard error) to a file


**here is the magic**

*>&*

this will connect some FD to some other FD in the same process.

for example you can redirect errors to standard output using : 

```
2>&1
```

## Tip

inverting grep matching using -v. very useful.

---

## Tee

The tee command, named after a "T-splitter" from plumbing pipes, duplicates data flowing through your pipes to any number of files provided on the command line.

always put everything from its input on its standard output and any file given as arguments to it.

example 
```sh
echo hi | tee pwn college
```

by running this, "hi" is printed on the console, and to the files pwn and college


## Proccess Substituation for input

as we have seen so far, we can mix and match different streams of processes.
but now we want to go a step further. we want to pass process as arguments !

actually we want their output be a file like input to another command. (not just simply pipe output of process to input of another process)

For reading from a commnad like a file we can use : 

```sh
<(command)
```

by doing this, we are creating a some uncommon temporary file. that is called `named pipe`. the output of process is written to this named pipe and other process can read from it (named pipe) like a normal file.

example :

```sh
hacker@dojo:~$ echo <(echo hi)
/dev/fd/63
hacker@dojo:~$
```
what happend ?
as we said, using "<(command)" run our program and redirect its output to a named pipe. the address to that named pipe is replaced in the argument. so what we see here "/dev/fd/63" is the address of named pipe that has the output of our command.

we have seen input process substitution. as expected, we have output process substitution.

```sh
>(command)
```
this time the output of some other process is written to a named pipe from which our command is reading.


some good example

```sh
/challenge/hack 1> >(/challenge/planet) 2> >(/challenge/the)
```

## Named Pipes

we can create our own `persistent` named pipes.

these act FIFO.

create a FIFO

```sh
mkfifo <name_of_pipe>
```

example 

```sh
hacker@dojo:~$ mkfifo my_pipe
hacker@dojo:~$ ls -l my_pipe
prw-r--r-- 1 hacker hacker 0 Jan 1 12:00 my_pipe
```

as you can see the file type of these are `p` which stands for pipe.


you can treat pipes like normal files. you can write to them and read from them.

features of FIFO :

1. No disk storage: FIFOs pass data directly between processes in memory - nothing is saved to disk
2. Ephemeral data: Once data is read from a FIFO, it's gone (unlike files where data persists)
3. Automatic synchronization: Writers block until the readers are ready, and vice-versa. This is actually useful! It provides automatic synchronization. Consider the example above: with a FIFO, it doesn't matter if cat myfifo or echo pwn > myfifo is executed first; each would just wait for the other. With files, you need to make sure to execute the writer before the reader.
4. Complex data flows: FIFOs are useful for facilitating complex data flows, merging and splitting data in flexible ways, and so on. For example, FIFOs support multiple readers and writers.


## Shell Variables

you can access them using $.
you can assign a variable using = (but be careful there shouldn't be any space before and after the =, this is correct : VAR=1, this is incorrect : VAR = 1)

variables are not passed to childs processes by default. to do that you should `export` them.

```sh
VAR=123
export VAR
```
now child processes can see $VAR. these become environment variables of child processes.

you can use `env` command to see all environment variables.
```sh
env
```


## Command Substitution

VAR=$(some command)

$() is called command substitution.

## Read input from user

you can use `read` command to read from input in shell and store the result in a variable.

example

```sh
read -p "INPUT: " MY_VARIABLE
INPUT: Hello!
```

-p option is used for prompting user.

read a file to a variable :

```sh
read MY_VAR < myfile
```


## Know the difference between SU and SUDO

SU : stands for "Substitute User"
SUDO : SU DO "Substitute User Do"

SU without any argument try to open a terminal as root user.
with a given username try to open a terminal as that user

SUDO instead try to run a command as root (or the specified user with -u)
not opening a whole terminal

SU authentication is password based. (you know how bad using password is)
SUDO authentication is different and is policy based (/etc/sudoers)


