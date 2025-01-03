# B. Windows Power Shell
## Intro

From the official Microsoft [page](https://learn.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.4): _“PowerShell is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework.”_

a scripting language built on the .NET framework.

object-oriented.

cross-platform.

`cmdlet` return objects that retain their properties and methods.

## Basics

Launch powershell.
- **Start Menu**: Type `powershell` in the Windows Start Menu search bar, then click on `Windows PowerShell` or `PowerShell` from the results.
- **Run Dialog**: Press `Win + R` to open the `Run` dialog, type `powershell`, and hit `Enter`.
- **File Explorer**: Navigate to any folder, then type `powershell` in the address bar, and press `Enter`. This opens PowerShell in that specific directory.
- **Task Manager**: Open the Task Manager, go to `File > Run new task`, type `powershell`, and press `Enter`.

 PowerShell commands are known as `cmdlets`

Verb-Noun:
- `Get-Content`: Retrieves (gets) the content of a file and displays it in the console.
- `Set-Location`: Changes (sets) the current working directory.

list all available cmdlets, functions, aliases, and scripts
and it's case-insensitive.
```
Get-Command
Get-Command -CommandType "Function"  # visit the propetry "commandtype"
```

get help page after `Update-Help`
```
Get-Help Get-Date
Get-Help Get-Date -examples
```

To make the transition easier for IT professionals, powershell offers aliases, like a shortcut, for common commands in `cmd.exe`. 
```
get-alias
```


find additional cmdlets online, from repository like `PowerShell Gallery`
```
find-module -name "powershell*"
```

install
```
install-module -name "powershellget"
```

```
$Password = Read-Host -AsSecureString
$params = @{
	Name        = 'User03'
	Password    = $Password
	FullName    = 'Third User'
	Description = 'Description of this account.'
}
```

## File System

`dir`
```
get-childitem
get-childitem -path "..."
```

`cd`
```
set-location
set-location -path ".\Documents"
```

create directories and files
```
New-Item -Path ".\captain-cabin\captain-wardrobe" -ItemType "Directory"
New-Item -Path ".\captain-cabin\captain-wardrobe\captain-boots.txt" -ItemType "File"
```

remove directory and file
```
Remove-Item -Path ".\captain-cabin\captain-wardrobe\captain-boots.txt"
Remove-Item -Path ".\captain-cabin\captain-wardrobe"
```

copy
```
Copy-Item -Path .\captain-cabin\captain-hat.txt -Destination .\captain-cabin\captain-hat2.txt
```

`cat`
```
Get-Content -Path ".\captain-hat.txt"
```

## Piping, Filtering, and Sorting

**Piping** is a technique used in command-line environments that allows the output of one command to be used as the input for another.
### Sort-Object
```
Get-ChildItem | Sort-Object Length
```
### Where-Object
`Where-Object` is an advanced output content controller.
```
Get-ChildItem | Where-Object -Property "Extension" -eq ".txt
```

**comparison operators** shared with `Python` and `Bash`.
- `-ne`: "**not equal**". This operator can be used to exclude objects from the results based on specified criteria.
- `-gt`: "**greater than**". This operator will filter only objects which exceed a specified value. It is important to note that this is a strict comparison, meaning that objects that are equal to the specified value will be excluded from the results.
- `-ge`: "**greater than or equal to**". This is the non-strict version of the previous operator. A combination of `-gt` and `-eq`.
- `-lt`: "**less than**". Like its counterpart, "greater than", this is a strict operator. It will include only objects which are strictly below a certain value.
- `-le`: "**less than or equal to**". Just like its counterpart `-ge`, this is the non-strict version of the previous operator. A combination of `-lt` and `-eq`.

`-like`
```
Get-ChildItem | Where-Object -Property "Name" -like "ship*"
```
### Select-Object
Select object, similar to `SELECT` in `SQL`
```
Get-ChildItem | Select-Object Name,Length
```
Notice the comma.

Find the largest file in current directory
```
get-childitem | sort-object length -descending | select-object -property name, length -first 1
```

### Select-String

`grep`
```
Select-String -Path ".\captain-hat.txt" -Pattern "hat"
```

Practice
```
Get-ChildItem | Where-Object -Property Length -gt 100
```

## System and Network Info

System info
```
Get-ComputerInfo
```

Local user
```
get-localuser
```

`Get-NetIPConfiguration` provides detailed information about the network interfaces on the system, including IP addresses, DNS servers, and gateway configurations.
```
Get-NetIPConfiguration
```

get IP addresses configured on system, even if they are not currently active.
```
Get-NetIPAddress
```

## Real-Time System Analysis

Get-Process, show processes and their usage
```
Get-Process
```

Get service info.
```
Get-Service
```

Show TCP connections, handy for incident response or malware analysis.
```
Get-NetTCPConnection
```

Get file hash. Same files will generate the same hashes, and the hashes maybe less than original file.
```
Get-FileHash -Path .\ship-flag.txt
```

## Scripting

**Scripting** is the process of writing and executing a series of commands contained in a text file, known as a script, to automate tasks that one would generally perform manually in a shell, like PowerShell.

Beyond this room.

Important cmdlet, `Invoke-Command`.
Run a command on remote computers.

Run local script
```sh
Invoke-Command -FilePath c:\scripts\test.ps1 -ComputerName Server01
```

Run a command `Get-Culture` .
```sh
Invoke-Command -ComputerName Server01 -Credential Domain01\User01 -ScriptBlock { Get-Culture }
```

## Conclusion

Commands all above can be simplified, such as omit options, in lowercase. When you try to run a command in a simple way but failed, you can use the verbose version of command.