Get Chrome browser notification preferences  
```powershell
(Get-Content "C:\Users\<user>\AppData\Local\Google\Chrome\User Data\Default\Preferences" -Raw | ConvertFrom-Json).profile.content_settings.exceptions.notifications | Format-Table -AutoSize  
```

Remove Chrome browser notification preference
```powershell
$preferencesPath="C:\Users\<user>\AppData\Local\Google\Chrome\User Data\Default\Preferences";$preferences=Get-Content -Path $preferencesPath -Raw | ConvertFrom-Json;$preferences.profile.content_settings.exceptions.notifications.PSObject.Properties.Remove("https://evilsite.com:443,*");$preferences | ConvertTo-Json -Depth 100 | Set-Content $preferencesPath  
```

Get Edge browser notification preferences  
```powershell
(Get-Content "C:\Users\<user>\AppData\Local\Microsoft\Edge\User Data\Default\Preferences" -Raw | ConvertFrom-Json).profile.content_settings.exceptions.notifications | Format-Table -AutoSize  
```

Remove Edge browser notification preferences  
```powershell
$preferencesPath="C:\Users\<user>\AppData\Local\Microsoft\Edge\User Data\Default\Preferences";$preferences=Get-Content -Path $preferencesPath -Raw | ConvertFrom-Json;$preferences.profile.content_settings.exceptions.notifications.PSObject.Properties.Remove("https://evilsite.com:443,*");$preferences | ConvertTo-Json -Depth 100 | Set-Content $preferencesPath  
```