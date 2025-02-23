Get phone numbers of all users
```powershell
Connect-MgGraph
Get-MgUser -All:$true -PipelineVariable user | ForEach-Object { Get-MgUserAuthenticationPhoneMethod -UserId $user.UserPrincipalName | Select-Object @{ N='UserPrincipalName'; E={ $user.UserPrincipalName }}, ID, PhoneNumber, PhoneType}

Get-MgUser -All:$true -PipelineVariable user | ForEach-Object {
    Get-MgUserAuthenticationPhoneMethod -UserId $user.UserPrincipalName | 
    Where-Object { $_.PhoneNumber -eq '+1 1111111111' } |
    Select-Object @{ N='UserPrincipalName'; E={ $user.UserPrincipalName }}, ID, PhoneNumber, PhoneType
}
```

Install module scoped to current user
```powershell
Install-Module AzureAD -Scope CurrentUser
```

Get all properties of user
```powershell
Connect-AzureAD
Get-AzureADUser -ObjectId somedude@null.null | Select-Object -Property *
```
