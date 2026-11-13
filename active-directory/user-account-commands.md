### Get AD user by SID
```powershell
$sid = Read-Host "SID"
Get-ADUser -Filter "ObjectSID -eq '$sid'"
```

### Create AD user
```powershell
New-ADUser -Name (Read-Host "Full Name") -GivenName (Read-Host "First name") -Surname (Read-Host "Last name") -SamAccountName (Read-Host "Username") -AccountPassword (Read-Host -AsSecureString "Enter Password") -Enabled $true
```

### Set account expiration date
```powershell
Set-ADUser -Identity (Read-Host "Username") -AccountExpirationDate (Get-Date).AddMonths(2).AddDays(5)
```

### Remove account expiration date
```powershell
Set-ADUser -Identity (Read-Host "Username") -AccountExpirationDate $null
```

### Get all OUs
```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

### Get User Objects in OU
```powershell
$searchBase = Read-Host "Search base"
Get-ADUser -Filter 'Enabled -eq $false' -SearchBase $searchBase -Properties DistinguishedName | Select-Object Name, DistinguishedName
```

### Update user object title
```powershell
Set-ADUser -Identity (Read-Host "Username") -Replace @{Title = (Read-Host "Title")}
```

### Get proxy addresses of user object
```powershell
Get-ADUser -Identity (Read-Host "Username") -Properties proxyAddresses | Select-Object -ExpandProperty proxyAddresses
```

### Update proxyAddresses with the new primary address
```powershell
$userIdentity = Read-Host "Username"
$newPrimaryAddress = Read-Host "Primary proxy address"
$secondaryAddress = Read-Host "Secondary proxy address"

Set-ADUser -Identity $userIdentity -Replace @{proxyAddresses = @($newPrimaryAddress, $secondaryAddress)}
```

### Update given property of user object
```powershell
Set-ADUser -Identity (Read-Host "Username") -Replace @{(Read-Host "Property") = (Read-Host "Value")}
```

### Get disabled user's DistinguishedNames
```powershell
Get-ADUser -Filter 'Enabled -eq $false' -Properties DistinguishedName | Select-Object DistinguishedName | Format-Table -AutoSize
```

### Move user object
```powershell
$userDN = (Get-ADUser -Identity (Read-Host "Username")).DistinguishedName
Move-ADObject -Identity $userDN -TargetPath (Read-Host "Path")

Move-ADObject -Identity (Read-Host "Object distinguished name") -TargetPath (Read-Host "Target path")
```

### Disable user
```powershell
Disable-ADAccount -Identity (Read-Host "Username")
```

### Enable user
```powershell
Enable-ADAccount -Identity (Read-Host "Username")
```

### Unlock Account
```powershell
Unlock-ADAccount -Identity (Read-Host "Username")
```

### Set account password
```powershell
Set-ADAccountPassword -Identity (Read-Host "Username") -NewPassword (Read-Host "Enter the new password" -AsSecureString)
```

### Force password change at logon
```powershell
Set-ADUser -Identity (Read-Host "Username") -ChangePasswordAtLogon $true
```

### Get workstations a user is allowed to logon to
```powershell
Get-ADUser -Filter { LogonWorkstations -ne "$null" } -Properties LogonWorkstations | Select-Object SamAccountName, LogonWorkstations
```

### Set workstations user can logon to
```powershell
Set-ADUser -Identity (Read-Host "Username") -LogonWorkstations (Read-Host "Workstations")
```

### Create new domain user
```powershell
New-ADUser -Name (Read-Host "Full name") -SamAccountName (Read-Host "Username") -UserPrincipalName (Read-Host "User principal name") -GivenName (Read-Host "First name") -Surname (Read-Host "Last name") -Enabled $true -AccountPassword (Read-Host "Password" -AsSecureString) -Path (Read-Host "Path") -EmailAddress (Read-Host "Email address")
```

### Get accounts trusted to authorize for delegation
```powershell
Get-ADUser -Filter {UserAccountControl -band 16777216} -Properties TrustedToAuthForDelegation | Select-Object Name, SamAccountName, TrustedToAuthForDelegation
```
-or-
```powershell
Get-ADUser -Filter * -Properties TrustedToAuthForDelegation | Where-Object { $_.TrustedToAuthForDelegation -eq $true } | Select-Object Name, SamAccountName, TrustedToAuthForDelegation
```

### Set the password to never expire
```powershell
Set-ADUser -Identity (Read-Host "Username") -PasswordNeverExpires $true
```

### Get enabled accounts
```powershell
Get-ADUser -Filter {Enabled -eq $true}
```

### Get locked accounts
```powershell
Search-ADAccount -LockedOut
```

### Get users never logged in
```powershell
$users = Get-ADUser -Filter * -Properties LastLogonTimestamp,Enabled,Description |
         Select-Object Name, SamAccountName, LastLogonTimestamp, Enabled, Description

$neverLoggedInUsers = $users | Where-Object { $_.LastLogonTimestamp -eq $null -and $_.Enabled -eq $true }

$neverLoggedInUsers | Format-Table -AutoSize
```

### Find accounts with a service principal name
```powershell
Get-ADUser -Filter { ServicePrincipalName -ne "$null" } -Properties ServicePrincipalName | Select-Object SamAccountName, ServicePrincipalName
```

### Get user object descriptions
```powershell
Get-ADUser -Filter * -Properties Description | Select-Object SamAccountName, Description
```

### Get domain admins
```powershell
Get-ADGroupMember -Identity "Domain Admins" -Recursive | Select-Object SamAccountName
```
