# Regular Expressions
## Introduction  
Regular expressions are some patterns to find content in a document.
## Charset
`[]`
It finds every occurrence of the pattern. But only for one character.

Use `-` to represent the range.

Use `^` to exclude the charset.
## Wildcards and optional characters
`.` match any single characters (except for line break)
`?` represents the character before `?` is optional.
## Metacharacters and repetitions
Metacharacters:
`\d` -- digit
`\D` -- non-digit
`\w` -- alphanumeric character, including `[A-Za-z0-9_]` 
`\W` -- non-alphanumeric character. 
`\s` -- whitespace character. (spaces, tabs, line breaks)
`\S` -- non-whitespace character.

Repetitions:
`{2}` match the preceding character (or metacharacter, or charset) two times in a row.
`{12}` - **exactly 12** times.  
`{1,5}` - **1 to 5** times.  
`{2,}` - **2 or more** times.  
`*` - **0 or more** times.  
`+` - **1 or more** times.
## Starts with/ ends with, groups, and either/ or
`^` starts with
`$` ends with
`()` groups
`(|)` either or
## Conclusion
Practice makes perfect.
Use the premade regular expression, instead of your own.
Think specific, but not too specific.