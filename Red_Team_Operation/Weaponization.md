# Weaponization

## Windows Scripting Host (WSH)
It is a Windows native engine, `cscript.exe` (for command-line scripts) and `wscript.exe` (for UI scripts), which are responsible for executing various Microsoft Visual Basic Scripts (VBScript), including vbs and vbe.   
```
Set shell = WScript.CreateObject("Wscript.Shell")
shell.Run("C:\Windows\System32\calc.exe " & WScript.ScriptFullName),0,True
```
```
c:\Windows\System32>wscript c:\Users\thm\Desktop\payload.vbs
c:\Windows\System32>cscript.exe c:\Users\thm\Desktop\payload.vbs 
```
Another trick. If the VBS files are blacklisted, then we can rename the file to `.txt` file and run it using `wscript` as follows,
```
c:\Windows\System32>wscript /e:VBScript c:\Users\thm\Desktop\payload.txt
```  

## An HTML Application (HTA)
