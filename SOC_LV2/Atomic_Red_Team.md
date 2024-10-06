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
    ```
    powershell -ExecutionPolicy bypass
    ```  

3. Import-Module コマンドレットを使用してモジュールをロードする。  
    ```
    Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force  
    $PSDefaultParameterValues = @{"Invoke-AtomicTest:PathToAtomicsFolder"="C:\AtomicRedTeam\atomics"}
    ```  
    ![Win10_WS-01-2024-10-06-13-23-45](https://github.com/user-attachments/assets/c6f4528d-c4b7-48b6-8466-d99039f378fd)  

4. うまくいったかテストしよう。  
    ```
    help Invoke-AtomicTest
    ```  
    ![Win10_WS-01-2024-10-06-13-42-35](https://github.com/user-attachments/assets/b7a2e26f-20a0-4a48-8590-1468db973b35)  

### テクニックのリストと詳細を調べる。  
- 一覧で表示  

    ```
    Invoke-AtomicTest  T1053.005 -ShowDetailsBrief  
    ```  
    ![Win10_WS-01-2024-10-06-14-44-29](https://github.com/user-attachments/assets/801091d5-69ad-44a0-a2ed-57516e49fb8e)    

    リストの一番上にある `T1053.005-1 Scheduled Task StartUp Script` をテストしてみることにする。まずは、テストの内容を調べる。`-TestNumber`オプションで番号を指定する。    
- 詳細を表示  
    ```
    Invoke-AtomicTest  T1053.005 -TestNumbers 1 -ShowDetails  
    ```  
    ![Win10_WS-01-2024-10-06-14-50-48](https://github.com/user-attachments/assets/9d519ed9-2db0-43ad-a083-850fa4a8a11e)  
    Descriptionには説明が書いてある。ユーザログオンあるいはシステムスタートアップ時に自動実行されるタスクが登録される。  
    Attack CommandsにはPowershell等で実行されるコマンドが書いてある。`T1053_005_OnLogon`, `T1053_005_OnStartup`というタスクが登録されるようだ。  
    ユーザログオン時に`calc.exe`が自動実行される。実際の攻撃ならば、calcの代わりにC2ビーコンやマルウェアが登録される。  

### 実行する。  
```
Invoke-AtomicTest  T1053.005 -TestNumbers 1  
```  
![Win10_WS-01-2024-10-06-15-01-21](https://github.com/user-attachments/assets/460f3ea4-6e22-4265-bb37-40be10ba8623)  
`schtasks`コマンドで調べてみても登録が確認できる。  

### 復旧する。  
AtomicRedTeamではレジストリの変更やファイルの作成がされるので、実行したら必ずきれいに復旧しよう。  
以下のコマンドでも不具合があることもあるので、VMのスナップショットによる復旧も考慮しよう。  
```
Invoke-AtomicTest  T1053.005 -TestNumbers 1 -CleanUp
```  
![Win10_WS-01-2024-10-06-16-15-31](https://github.com/user-attachments/assets/49ef34af-849e-45fc-9bc8-106c77704844)  


## 調査しよう。  
1. Security Onion   
    まずはKibanaでクエリを発行し調査してみる。  
    Windows Advanced Audit Policyが適切に設定されていれば、Event ID 4698 (Scheduled Task Creation)を使うことができる。（本環境では設定されていない。）また、スケジュールされたタスクに関連するコマンドを検索するために、以下のキーワードを使用する。：`schtasks and Register-ScheduledTask（PowerShell）`  
    ```
    host.name: WKSTN-* AND (winlog.event_id: 4698 OR (*schtasks* OR *Register-ScheduledTask*))
    ```  
    ![kali-linux-2023 4-vmware-amd64-2024-10-06-16-46-17](https://github.com/user-attachments/assets/a633bec9-986a-4e21-ab7d-342be31b843e)  

    表示コラムを調整する。  
    ![kali-linux-2023 4-vmware-amd64-2024-10-06-15-47-24](https://github.com/user-attachments/assets/47c0a9d2-e0da-41bd-a3d0-597f8c47ff04)  
    AtomicRedTeamで実行したスケジュールドタスク(`T1053_005_OnLogon`等)が確認できる。  
2. VelociRaptor  
    VelociRaptaorのVQLでも同様の調査ができる。（別資料を参照）  
    今回はVelociraptorからPowershellで調べてみよう。  
    ![kali-linux-2023 4-vmware-amd64-2024-10-06-15-37-50](https://github.com/user-attachments/assets/bae10004-8067-4e3b-9e4f-6afe9a3f9a72)  

### 同じ要領で他のテクニックもテストしてみよう。その際メモをとってもらいたい。いいものはプレイブックに登録します。  


