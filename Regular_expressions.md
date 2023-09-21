# Regular expressions

## Charasets
`[abc]` : match `a`, `b` and`c`  
`[abc]zz` : match `azz`, `bzz`, `czz`  
`[a-c]zz` : match `azz`, `bzz`, `czz`  
`[a-cx-z]` : match `azz`, `bzz`, `czz`, `xzz`, `yzz`, `zzz`  
`[a-zA-Z]` : match any single letter(lowercase or uppercase)  
`file[1-3]` : match `file1`, `file2`, `file3`  
`[^k]ing` : match NOT `king`  
`[^a-c]at` : match NOT `aat`, `bat`, `cat`  

## Wildcards and optional characters
`.` : match any single characlter(Wildcard)  
`a.c` : match `aac`, `abc`, `acc`, ..., `a!c`...  
`...[^n-z]` : match every 4-letter string that doesn't end in any letter from `n` to `z`.    
`?` : optional  
`abc?` : match `ab`, and `abc`, since `c` is optional.  
`\` : escape  
`a\.c` : match `a.c`  

## Metacharacters and repetitions
`\d` matches a digit, like `9`  
`\D` matches a non-digit, like `A` or `@`  
`\w` matches an alphanumeric character, like `a` or `3` and `_`   
`\W` matches a non-alphanumeric character, like `!` or `#`  
`\s` matches a whitespace character (spaces, tabs, and line breaks)  
`\S` matches everything else (alphanumeric characters and symbols)  





