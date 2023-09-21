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
