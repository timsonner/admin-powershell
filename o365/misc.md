Get license status of user
```powershell
Install-Module MSOnline -Scope CurrentUser
Connect-MsolService
Get-MsolUser -UserPrincipalName bill@contoso.com | Select-Object -Property isLicensed
```

Get user info
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
Connect-Graph
Get-MgUser -UserId bill@contoso.com | Select-Object -Property *
```

Install AZ module using elevated credentials
```powershell
$credential = Get-Credential
$command = "Install-Module -Name Az -Repository PSGallery -Force"
Start-Process -FilePath $command -Credential $credential
```