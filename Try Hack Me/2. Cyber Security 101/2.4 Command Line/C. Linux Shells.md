# C. Linux Shells
## Review
Most Linux distributions use Bash (Bourne Again Shell) as their default shell.

Let's look back the most important commands.

`pwd` Print Working Directory
`cd`   Change Directory
`ls`
`cat` read contents of file
`grep pattern filename` 
## Types of Shells 
### Intro
`$SHELL` default shell

`cat /etc/shells` all the installed shells on Linux

Change the default shell (Reboot to make effects)
```
chsh -s /bin/zsh
```

### Bash `Bourne Again Shell`
- scripting
- Tab completion
- use up and down keys to switch to previous commands. Alternative: `history`
### Fish `Friendly Interactive Shell`
- auto correction
- customize command prompt
- color
- scripting, tab, history
### Z Shell `zsh`
- advanced tab completion 
- spell correction
- extensive customization, slower than other shell
- history
## Shell Scripting and Components
### Basic
A shell script is nothing but a set of commands.

Create a script
```sh
nano first_script.sh
```

Every script should start from shebang.
Shebang is a sting specify what shell will be used with.
```
#!/bin/bash
```
### Variables
`read` read input from the user
```sh
#!/bin/bash
echo "Hey, what’s your name?"
read name
echo "Welcome, $name"
```

In the double quotes, the `$var` will be extracted.
But in the single quotes, the `$var` won't be extracted.

Then you need add permission.
```sh
chmod +x ./variable_script.sh
```
### Loops
```sh
#!/bin/bash
for i in {1..10};
do
echo $i
done
```
### Conditional Statements
```sh
#!/bin/bash
echo "Please enter your name first:"
read name
if [ "$name" = "Stewart" ]; then
        echo "Welcome Stewart! Here is the secret: THM_Script"
else
        echo "Sorry! You are not authorized to access the secret."
fi
```
### Comments
Start with a `#`, will not effect the script work.
## Locker Script
```sh
#!/bin/bash

# init
username=""
company=""
pin=""

# prompt
for i in {1..3};
do
        if [ $i -eq 1 ]; then
                echo "Enter your username:"
                read username
        elif [ $i -eq 2 ]; then
                echo "Enter your company:"
                read company
        else
                echo "Enter your pin:"
                read pin
        fi
done

# check
if [ "$username" = "passion"  ] && [ "$company" = "zzu" ] && [ "$pin" = "1234" ]; then
        echo "Open your locker!!!"
else
        echo "Authentication Denied!!!"
fi
```
## Flag Hunter

Switch to root
```sh
sudo su
```

```sh
#!/bin/bash
  
# Defining the directory to search our flag
directory="/var/log"

# Defining the flag to search
flag="thm-flag01-script"

echo "Flag search in directory: $directory in progress..."

# Defining for loop to iterate over all the files with .log extension in the defined direc
tory
for file in "$directory"/*.log; do
    # Check if the file contains the flag
    if grep -q "$flag" "$file"; then
# Print the filename
	echo "Flag found in: $(basename "$file")"
    fi
done
```
