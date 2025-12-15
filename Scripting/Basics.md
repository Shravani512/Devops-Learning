
## Shell Scripting

Shell scripting is all about automating tasks — and variables are the building blocks that make scripts dynamic and reusable.
A variable is simply a name that stores a value (like a number, string, or command output) for later use.

1. User-defined variables
2. Predefined (system) variables
3. Arguments (positional parameters)

##### User-Defined Variables

These are the variables created by the user in the script or terminal.
They are used to store custom values like filenames, messages, or numbers.

variable_name=value
👉 No spaces around = sign.

eg. 
```
#!/bin/bash
name="Shravani"
age=24

echo "My name is $name"
echo "My age is $age"

output: 
My name is Shravani
My age is 24
```

##### Predefined (System) Variables

These are built-in variables automatically created by the shell.
They hold information about the environment, system, and shell process.

###### Few perdefined variables
$HOME	         -> Home directory of the current user
$PWD	         -> Current working directory
$USER	         -> Logged-in username
$HOSTNAME	     -> System hostname
$SHELL	       -> Default shell being used
$RANDOM        -> Returns a random number
$#	           -> Number of arguments passed
$?	           -> Exit status of the last command

eg.
```
#!/bin/bash
echo "Home directory: $HOME"
echo "Current directory: $PWD"
echo "User: $USER"
echo "Shell: $SHELL"
echo "Random Number: $RANDOM"

output:
Home directory: /home/ubuntu
Current directory: /home/ubuntu/scripts
User: ubuntu
Shell: /bin/bash
Random Number: 23849
```

##### Arguments (Positional Parameters)

When you run a script, you can pass arguments from the command line.
These are accessed using positional parameters like $1, $2, $3, etc.

./scriptname.sh arg1 arg2 arg3

eg.
```
#!/bin/bash
echo "Script Name: $0"
echo "First Argument: $1"
echo "Second Argument: $2"
echo "Total Arguments: $#"

run command: ./myscript.sh Linux Scripting

output:
Script Name: ./myscript.sh
First Argument: Linux
Second Argument: Scripting
Total Arguments: 2

```
###### Overview:
User-Defined	    -> name="Shravani"	          Created by user manually
Predefined	      -> $USER, $PWD, $RANDOM	      System/environment variables
Arguments	        -> $1, $2, $#, $@	            Passed from command line

###### To see all environment (predefined) variables using:
printenv
