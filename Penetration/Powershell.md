# Powershell

powershell 命令就是 `cmdlet`，返回对象的属性和方法。

```sh
Get-Content # cat
Set-Location # cd
```

```sh
Get-Command
Get-Command -CommandType "Function"  # visit the propetry "commandtype"
```

```sh
Get-Help Get-Date
Get-Help Get-Date -examples
```

```sh
Get-Alias
```

Find additional cmdlets online.
```sh
find-module -name "powershell*"
```

```sh
install-module -name "powershellget"
```

```sh
$Password = Read-Host -AsSecureString
$params = @{
	Name        = 'User03'
	Password    = $Password
	FullName    = 'Third User'
	Description = 'Description of this account.'
}
```

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

```
Get-ChildItem | Sort-Object Length
```


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


```
Get-ChildItem | Select-Object Name,Length
```

`grep`
```
Select-String -Path ".\captain-hat.txt" -Pattern "hat"
```

```
Get-ChildItem | Where-Object -Property Length -gt 100
```

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

Run local script
```sh
Invoke-Command -FilePath c:\scripts\test.ps1 -ComputerName Server01
```

Run a command `Get-Culture` .
```sh
Invoke-Command -ComputerName Server01 -Credential Domain01\User01 -ScriptBlock { Get-Culture }
```