module "Computer 101" in pwn.college

some basic and general stuff

1. each architecture has its own set of instructions (and assembly language)
2. today x86 and ARM are dominant architectures
3. around 10-20 general purpose registers in common architectures.
4. x86\_64 version has 16  general purpose registers

## Simple Instructions and Syntax of X86

it seems the general structure is 

```
INST DST, SRC
```

that is destination comes before source

## Syscall

your programs interact with the operating system (via the CPU, of course) using the syscall (system call)

Each system call is indicated by a syscall number, counting upwards from 0, and your program invokes a specific syscall by moving its syscall number into the rax register and invoking the syscall instruction.

example
```assembly
mov rax, 43
syscall
```

### passing arguments to syscall

first argument is read from `rdi` register

example (exit system call with exit code of 42, 60 is syscall number of exit) 
```assembly
mov rdi, 42
mov rax, 60
syscall
```


## Create an assembly program in x86

as you know , a normal program prepartion flow (for compiled languages) is as follows ( commands are given for a sample C program ) : 

1. preprocess (cpp, C pre-processer) : pure C code file (no comment, # directives and macros, ...) (.i) -> gcc -E program.c -o program.s
2. compilation (cc1, C compiler) : create an assembly file (.s, .S) -> gcc -S program.i -o program.s
3. assemble (as, assembler for x86) : create an object file (.o) -> gcc -c program.s -o program.o
4. Linking (ld, link editor) : create an executable or shared object -> gcc program.o -o program


because we are writting in assembly there is no need to undertake first two steps. instead we should take care of creating a valid assembly code.

to make the assembler understand the syntax of our assembly code we should use `assembly directives`.

### .intel\_syntax noprefix

this directive tells the assembler that we are using intel syntax (instead of AT&T syntax). the noprefix argument tells that we are not using prefixes to instructions to indicate the syntax. everything by default is intel syntax, nothing more.

we should use this at the begining of our assembly code.

example 

```assembly
.intel_syntax noprefix
mov rdi, 42
mov rax, 60
syscall

```

now we can easily create the object file using `as`

```sh
as -o program.o program.s
```

the final step to make an object file (or a set of object files) executable is undergoing process of linking. that is done using `ld`

```sh
ld -o program program.o
```

### \_start

each program has an entry point. the very begining funcation that should gets executed.

each program has one and only one entry point, and in assembly it is marked with `_start` symbol. this symbol should be a global symbol so that other parts of program recognize it and also linker recognize it.

you can define global symbols in assembly using `.global` directive

```assembly
.global _start
```

then you can mark the starting instruction of your program by labeling it `_start`.

```assebmly
.intel_syntax noprefix
.global _start

_start:
mov rdi, 42
mov rax, 60
syscall
```

if you do not specify an entry point (that is \_start symbol) in your program, by defualt linker make your first instruction in file as the first instruction that should be exectued.

## Accessing Memory

### direct memory address
using raw address 

```assembly
mov rax, [some_memory_address]
mov rax, [124516] # read from memory
mov [12351], 80 # write to memory
```
### indirect memory address

address of memory is stored in a register

```assembly
mov rax, [some_resigter]
mov rax, [rdi]
```

### indirect mermory address with offset

```assembly
mov rax, [rdi+offset]
mov rax, [rdi+8] # 8 bytes after where the rdi is referencing
```

