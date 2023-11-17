# Regular expressions

[https://regexr.com/](https://regexr.com/)  

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
`z{2}` : match `zz`  
`{12}` - exactly 12 times.  
`{1,5}` - 1 to 5 times.  
`{2,}` - 2 or more times.  
`*` - 0 or more times.  
`+` - 1 or more times.  
`[abc]{1,3}[01]{4}` : match `ab0001`, `bb0000`, `abc1000`, `cba0110`, `c0000`  
`[fF]ile\d{1,2}` : match `File01`, `File2`, `file12`, `File20`, `File99`  
`kali\s+tools` : match `kali tools`, `kali     tools`  
`\w{5}\W` : match `notes~`, `stuff@`, `gtfob#`, `lmaoo!`  
`\S*\s*\S*` : match `2f0h@f0j0%!     a)K!F49h!FFOK`  
`\S{8}[^!]` : match every 9-character string (with letters, numbers, and symbols) that doesn't end in a "!" sign.  
`\.?\w+` : match `.bash_rc`, `.unnecessarily_long_filename`, and `note1`  

`\b([0-9]{1,3}\.){3}[0-9]{1,3}\b` : match `126.47.40.189`, IP Address  

## Regex with CyberChef
`\b([0-9]{1,3}\.){3}[0-9]{1,3}\b` to search for values that are IP addresses.  
 <img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/355300c7-00c5-470c-a910-8519af9d0aa7">   





