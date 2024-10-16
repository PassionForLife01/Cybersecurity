In most operating systems, including Linux, every directory has two implicit entries that you can reference in paths: `.` and `..`. The first, `.`, refers right to the same directory, so the following absolute paths are all identical to each other:

- `/challenge`
- `/challenge/.`
- `/challenge/./././././././././`
- `/./././challenge/././`

The following relative paths are also all identical to each other:

- `challenge`
- `./challenge`
- `./././challenge`
- `challenge/.`

Linux explicitly avoids automatically looking in the current directory when you provide a "naked" path. Consider the following:

```console
hacker@dojo:~$ cd /challenge
hacker@dojo:/challenge$ run
```

This will _not_ invoke /challenge/run. This is actually a safety measure: if Linux searched the current directory for programs every time you entered a naked path, you could accidentally execute programs in your current directory that happened to have the same names as core system utilities!

Note that the expansion of `~` is an _absolute_ path