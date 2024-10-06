# Atomic Red Team  
![ART](https://github.com/user-attachments/assets/bbe7dc66-8080-451c-8cb7-2bd187fe79c1)  

https://tryhackme.com/r/room/atomicredteam  

Atomic Red Teamとは Atomic Red Teamは、セキュリティテストと脅威のエミュレーションを実行するためのフレームワークを提供するオープンソースプロジェクトです。  

今回は、永続化のテクニック　`T1053.005: Scheduled Task/Job: Scheduled Task` を調査していこう。  
https://attack.mitre.org/techniques/T1053/005/   


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

### 準　備  

まずは準備をしていこう。これはWindowsホストが再起動するたびに実施する。  
`"C:\AtomicRedTeam\AtomicRedTeam.txt"`にコマンドは書いてあるので参考に。  
1. PowerShellをAdministratorで開く。  
![Win10_WS-01-2024-10-06-13-16-28](https://github.com/user-attachments/assets/a96fc89c-7702-4823-83c1-7bb97c257ab3)  

2. モジュールの実行中のセキュリティ警告を全て無視する。  
    `powershell -ExecutionPolicy bypass`
3. Import-Module コマンドレットを使用してモジュールをロードする。  
    ```
    Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force  
    $PSDefaultParameterValues = @{"Invoke-AtomicTest:PathToAtomicsFolder"="C:\AtomicRedTeam\atomics"}
    ```  





4. うまくいったかテストしよう。  
    `help Invoke-AtomicTest`  

### テクニックのリストと詳細を調べる。  
```
Invoke-AtomicTest  T1053.005 -ShowDetailsBrief  
```  

```
Invoke-AtomicTest  T1053.005 -TestNumbers 1 -ShowDetails  
```

### 実行する。  
```
Invoke-AtomicTest  T1053.005 -TestNumbers 1  
```  

### 復旧する。  
```
Invoke-AtomicTest  T1053.005 -TestNumbers 1 -CleanUp
```  

## 調査しよう。  
1. Security Onion   
    まずはKibanaでクエリを発行し調査してみる。  
    Windows Advanced Audit Policyが適切に設定されていれば、Event ID 4698 (Scheduled Task Creation)を使うことができる。（本環境では設定されていない。）また、スケジュールされたタスクに関連するコマンドを検索するために、以下のキーワードを使用する。：`schtasks and Register-ScheduledTask（PowerShell）`  
    ```
    host.name: WKSTN-* AND (winlog.event_id: 4698 OR (*schtasks* OR *Register-ScheduledTask*))
    ```


    表示コラムを調整する。  






    AtomicRedTeamで実行したスケジュールドタスクが確認できる。  
2. VelociRaptor  
    VelociRaptaorのVQLでも同様の調査ができる。（別資料を参照）  
    今回はVelociraptorからPowershellで調べてみよう。  



### 
