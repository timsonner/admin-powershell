### Clear Windows update cache
```powershell
Stop-Service -Name wuauserv -Force;Stop-Service -Name bits -Force;Stop-Service -Name cryptSvc -Force;Stop-Service -Name trustedinstaller -Force
Remove-Item -Path "C:\Windows\SoftwareDistribution" -Recurse -Force
Start-Service -Name wuauserv;Start-Service -Name bits;Start-Service -Name cryptSvc;Start-Service -Name trustedinstaller
```

### Clear user caches
```powershell
Remove-Item -Path "C:\Users\*\AppData\Local\Temp\*" -Recurse -Force
```

### Clear Teams cache  
```  
C:\Users\<user>\appdata\local\Packages\MSTeams_*\LocalCache> rm *
```  
