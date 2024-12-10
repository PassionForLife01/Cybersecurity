# Vim
How do we save and quit, for all active tabs?
```vim
:wqa
```

How do we write the file, but don't exit- as root?
```
:w !sudo tee %
```

Substitute
```
:s/old/new
:s/old/new/g
:s/old/new/gc

type   :#,#s/old/new/g    where #,# are the line numbers of the range
                               of lines where the substitution is to be done.
     Type   :%s/old/new/g      to change every occurrence in the whole file.
     Type   :%s/old/new/gc     to find every occurrence in the whole file,
                               with a prompt whether to substitute or not.
```