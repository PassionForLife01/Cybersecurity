# Bash Scripting
## Introduction to Bash
Bash is a scripting language that runs with terminal on Linux or MacOS.
It is actually a sequence of commands.
## Sample
Start with a comment:
```sh
#!/bin/bash
```
To let OS know that you are using the Bash.

A simple script.
```sh
#!/bin/bash
echo "Hello, world!"
whoami
id
```
Do not need semicolon at the end of line.
## Variable
```sh
name="Jammy"
```
To declare a variable, you cannot leave some spaces between the variable name, the `=` and the value.

```sh
name="Jammy"
echo Hello, $name
```

Debug.
```sh
bash -x ./file.sh
```
It will show the command, followed by the output.
![[Pasted image 20241128101936.png]]
`+` -- a command was executed.
`-` -- a command was not executed.

If you want to debug a certain snippet of code:
![[Pasted image 20241128101805.png]]

Tips:
You can insert the variable into some places, using `$`.

In the double quotes, the `$var` will be extracted.
But in the single quotes, the `$var` won't be extracted.
## Parameters
Command-Line Argument:
```sh
#!/bin/bash
echo "Hello, $1"
```

`$#` -- The number of parameters.

`$0` -- The first argument, including the filename of the script.

Interactively input.
```sh
#!/bin/bash
echo "Enter your name:"

read name

echo "Your name is $name"
```

## Arrays
Declare a array.
```sh
#!/bin/bash

transport=('car' 'train' 'bike' 'bus')
```
Leave the space between two elements.

Dump the elements of the array.
```sh
echo "${transport[@]}"
```

Visit single element.
```sh
echo "${transport[1]}"
```

Delete a element, (literally set the element to NULL, which means that space exists, but empty)
```sh
unset transport[1]
```

Change, or fill in.
```sh
transport[1]='trainride'
```

## Conditionals
If statement:
```sh
if [ sth comparison sth ]
then
	code1
else
	code2
fi
```
Notice that we need leave a space on both sides of the text, this is the bash syntax.

```sh
#!/bin/bash

count=10
if [$count -eq 10]
then
    echo "true"
else
    echo "false"
fi
```

These maybe are used in comparison on integer. You can also use the `=` `<` `>`.

|          |             |
| -------- | ----------- |
| Operator | Description |
| -eq      | ==          |
| -ne      | !=          |
| -gt      | >           |
| -lt      | <           |
| -ge      | >=          |

`-f` checked if the file existed.
`-w` checked if the file writable.
`-r` checked if the file executable.
`-d` checked if the file is a directory.