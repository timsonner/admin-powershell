Add local user
```powershell
New-LocalUser -Name (Read-Host "Username") -Password (Read-Host "Password" -AsSecureString)
```

Set local user password  
```powershell
Set-LocalUser -Name (Read-Host "Username") -Password (Read-Host "Password" -AsSecureString)
```

Remove local user  
```powershell
$sid = (Get-CimInstance -ClassName Win32_UserAccount | Where-Object { $_.Name -eq "johnm" }).SID
reg delete "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$sid" /f
Remove-Item -Path "C:\Users\johns" -Recurse -Force
```

Get SID based on username
```powershell
Get-CimInstance Win32_UserAccount -Filter Read-Host ("Username") | Select-Object Name, SID
```

Get SID based on group name
```powershell
Get-CimInstance Win32_GroupAccount -Filter Read-Host ("Group") | Select-Object Name, SID
```

Get local admins
```powershell
Get-LocalGroupMember Administrators | Select-Object Name, PrincipalSource | Where-Object { $_.PrincipalSource -eq "Local" }
```

Get local users
```powershell
$localUsers = Get-CimInstance Win32_UserAccount -Filter "LocalAccount='True'"

foreach ($user in $localUsers) {
    Write-Host "Username: $($user.Caption)"
    Write-Host "Full Name: $($user.FullName)"
    Write-Host "Description: $($user.Description)"
    Write-Host "SID: $($user.SID)"
    Write-Host "Disabled: $($user.Disabled)"
    Write-Host "Password Expires: $($user.PasswordExpires)"
    Write-Host "Last Logon: $($user.LastLogin)"
    Write-Host "----------------------------------------"
}
```