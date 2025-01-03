# A. Windows Command Line
## Basic System Info

Show environment variable
```
set
```

Version
```
ver
```

Detailed info about system, including memory, full OS.
```
systeminfo
```

Use pipe to display content page by page.
```
driverquery | more
```
Press space to turn to next page.
`Ctrl + C` to exit.

```
cls
```
## Network Troubleshooting

IP info
```
ipconfig
```
More info, including DHCP and MAC (physical) address.
```
ipconfig /all
```

Detect if the host is live, by sending a ICMP packet (based on IP, not TCP/UDP).
```
ping example.com
```

Trace the route from here to there.
```
tracert example.com
```

DNS lookup
default DNS server
``` 
nslookup example.com
```

specific DNS server
```
nslookup example.com 10.0.0.1
```

Check network state, show connections, listening ports, and what program is using network. 
```
netstat    # connections
netstat -a # listenging ports, even unconnected things
netstat -b # programs
netstat -o # PID
netstat -n # show the numeric form of address
netstat -abon # combine together
```

## File and Disk Management

current directory
```
cd 
```

list of files and directory
```
dir
dir /a   # all files, including hidden files and system file
dir /s   # display all the files in current folder and subfolder
```

`dir /s` 装\*神器

show the tree-like hierarchy of current folder and subfolder.
```
tree
```

you know
```
cd
mkdir
rmdir
```

dump the text even fill your terminal
```
type
```

show content line by line (`Enter`) or page by page (`Space`)
```
more
```

copy
```
copy test.txt test2.txt
```

move
```
move
```

delete
```
del
erase
```

wildcard `*`
```
del /*
copy *.md C:\Markdown
```

## Task and Process Management

Show the task list.
```
tasklist
tasklist /?
tasklist /FI "imagename eq sshd.exe"
```

Kill the task by PID.
```
taskkill /PID 4567
```
## Conclusions
- `chkdsk`: checks the file system and disk volumes for errors and bad sectors.
- `driverquery`: displays a list of installed device drivers.
- `sfc /scannow`: scans system files for corruption and repairs them if possible.

help page, followed by `/?`

about `more`
- Display text files: `more file.txt`
- Pipe long output to view it page by page: `some_command | more`

`shutdown`
```
shutdown /s  # shutdown
shutdown /r  # restart
shutdown /a  # abort shutdown
```

