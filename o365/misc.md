### Get license status of user
```powershell
Install-Module MSOnline -Scope CurrentUser
Connect-MsolService
Get-MsolUser -UserPrincipalName (Read-Host "User principal name") | Select-Object -Property isLicensed
```

### Get user info
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
Connect-MgGraph
Get-MgUser -UserId (Read-Host "User id") | Select-Object -Property *
```

### Install AZ module using elevated credentials
```powershell
$credential = Get-Credential
Start-Process -FilePath "powershell.exe" -Credential $credential -ArgumentList "-NoProfile","-Command","Install-Module -Name Az -Repository PSGallery -Force"
```
