### Get Chrome browser notification preferences
```powershell
$user = Read-Host "Username"
$preferencesPath = "C:\Users\$user\AppData\Local\Google\Chrome\User Data\Default\Preferences"
(Get-Content $preferencesPath -Raw | ConvertFrom-Json).profile.content_settings.exceptions.notifications | Format-Table -AutoSize
```

### Remove Chrome browser notification preference
```powershell
$user = Read-Host "Username"
$origin = Read-Host "Notification origin"
$preferencesPath = "C:\Users\$user\AppData\Local\Google\Chrome\User Data\Default\Preferences"
$preferences = Get-Content -Path $preferencesPath -Raw | ConvertFrom-Json
$preferences.profile.content_settings.exceptions.notifications.PSObject.Properties.Remove($origin)
$preferences | ConvertTo-Json -Depth 100 | Set-Content $preferencesPath
```

### Get Edge browser notification preferences
```powershell
$user = Read-Host "Username"
$preferencesPath = "C:\Users\$user\AppData\Local\Microsoft\Edge\User Data\Default\Preferences"
(Get-Content $preferencesPath -Raw | ConvertFrom-Json).profile.content_settings.exceptions.notifications | Format-Table -AutoSize
```

### Remove Edge browser notification preferences
```powershell
$user = Read-Host "Username"
$origin = Read-Host "Notification origin"
$preferencesPath = "C:\Users\$user\AppData\Local\Microsoft\Edge\User Data\Default\Preferences"
$preferences = Get-Content -Path $preferencesPath -Raw | ConvertFrom-Json
$preferences.profile.content_settings.exceptions.notifications.PSObject.Properties.Remove($origin)
$preferences | ConvertTo-Json -Depth 100 | Set-Content $preferencesPath
```
