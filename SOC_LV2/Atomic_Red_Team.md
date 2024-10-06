# Atomic Red Team  
![ART](https://github.com/user-attachments/assets/bbe7dc66-8080-451c-8cb7-2bd187fe79c1)  

https://tryhackme.com/r/room/atomicredteam  

Atomic Red Teamとは Atomic Red Teamは、セキュリティテストと脅威のエミュレーションを実行するためのフレームワークを提供するオープンソースプロジェクトです。  


## Invoke-AtomicRedTeam  
AtomicRedTeamのバイナリはWindowsホスト(ws-01, ws-02)に格納されている。  
```
PS C:\Users\Allyce.Karon> dir C:\AtomicRedTeam


    Directory: C:\AtomicRedTeam


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          9/8/2024   8:27 PM                atomics
d-----          9/7/2024  10:59 PM                ExternalPayloads
d-----          9/8/2024   8:26 PM                invoke-atomicredteam
-a----          9/8/2024   8:34 PM      167966126 atomic-red-team-master.zip
-a----          9/8/2024   8:53 PM            995 AtomicRedTeam.txt
```  

AtomicRedTeamはPoweshellコマンドInvoke-AtomicRedTeamで実行する。  
まずは準備をしていこう。これはWindowsホストが再起動するたびに実施する。  
`"C:\AtomicRedTeam\AtomicRedTeam.txt"`にコマンドは書いてあるので参考に。  
1. PowerShellをAdministratorで開く。  

2. モジュールの実行中のセキュリティ警告を全て無視する。  
    `powershell -ExecutionPolicy bypass`
3. Import-Module コマンドレットを使用してモジュールをロードする。  
    ```
    Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force`  
    `$PSDefaultParameterValues = @{"Invoke-AtomicTest:PathToAtomicsFolder"="C:\AtomicRedTeam\atomics"}
    ```  





4. うまくいったかテストしよう。  
    `help Invoke-AtomicTest`  
