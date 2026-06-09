this file is gathered by me. nothing standard or anything like that. also this isn't a module in anywhere.
it is just the things that I encounter.

## Linux

### .bashrc

> writable .bashrc

you know that upon start up of a bash, the /home/user/.bashrc (that is .bashrc in the home directory of user) 
is invoked before anything.

if you have write access to that file its over for that user.


> readable .bashrc

less critical than writable one, but this one can be dangerous too.
there maybe some special settings, keys, passwords, ... in the .bashrc file that developers put there for convenience 
e.g. exporting a API key env.

### ps aux

sometimes, scripts or programs invoked by other users are of interest to us. maybe their invokation has leakage of information (for example passing password as argument) or the program/script itself is vulnerable.

so check processes on the system carefully.

### shared directories

directories that are world writable can be dangerous.

if a directory is world-writable you can create/remove files.
even if you don't have permission to write to a certain file, you can easily replace it by create a decoy one and removing the original file.

remember to set the `sticky bit` (chmod +t directory) on these environments.

**also you can create symbolic links to sensitive files, which normally can not be accessed with your permissions**

### PATH and command hijacking

if there is a chance to manipulate PATH (for example using .bashrc of target) you can change command behavior at your will.
re-difinition of common commands (like cat, ls, ...) and adding your own malicious scripts.


