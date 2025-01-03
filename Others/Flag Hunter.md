## Code
```sh
#!/bin/bash

directory="$1"

for file in "$directory"/*;
do
	if [ -f "$file" ]; then
		if grep -q -E "flag\{[^\}]+\}" "$file"; then
			echo "Found in $file"
			grep -E "flag\{[^\}]+\}" "$file"
		fi
    fi
done
```

## Gains
- Directory with more slashes can work. `//flag` `///flag`. So just feel free to add slash. 
- This is wrong: `if grep -q -E [ "flag\{[^\}]+\}" "$file" ]`. The square bracket represents the `test` command, cannot combine with `grep`.
What is test command ? [[Test Command in Linux]]

