### Get AD user by SID
```powershell
$sid = "<SID goes here>"
Get-ADUser -Filter {ObjectSID -eq $sid}
```

### New-AdUser  
```powershell
New-ADUser -Name (Read-Host "Full Name") -GivenName (Read-Host "First name") -Surname (Read-Host "Last name") -SamAccountName (Read-Host "Username") -AccountPassword (Read-Host -AsSecureString "Enter Password") -Enabled $true
```

Set account expiration date  
```powershell
$currentDate = (Get-Date)

$newExpirationDate = $currentDate.AddMonths(2).AddDays(5)

$Username = (Read-Host "Username")
Set-ADUser -Identity $Username -AccountExpirationDate $newExpirationDate
```

Remove account expiration date
```powershell
Set-ADUser -Identity (Read-Host "username") -AccountExpirationDate $null
```

List OUs
```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

List User Objects in OU  
```powershell
Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=<OU>,DC=<Domain>,DC=<local, com, etc>" -Properties Distinguishe
dName | Select-Object Name, DistinguishedName
```  

Update user object title  
```powershell
Set-ADUser -Identity (Read-Host "Username") -Replace @{Title = 'Super Fancy New Title'}
```  

Get proxy addresses
```powershell
Get-ADUser -Identity (Read-Host "Username") -Properties proxyAddresses | Select-Object -ExpandProperty proxyAddresses
```

Update proxyAddresses with the new primary address
```powershell  
# Define user and proxyAddresses
$userIdentity = "username"
$newPrimaryAddress = "SMTP:username@google.com"
$secondaryAddress = "smtp:username@apple.com"

Set-ADUser -Identity $userIdentity -Replace @{proxyAddresses = @($newPrimaryAddress, $secondaryAddress)}
```  

Change given property of user object
```powershell  
Set-ADUser -Identity (Read-Host "Username") -Replace @{(Read-Host "Property") = (Read-Host "Value")}
```  

Find disabled user DistinguishedNames
```powershell 
Get-ADUser -Filter {Enabled -eq $false} -Properties DistinguishedName | Select-Object DistinguishedName | Format-Table -AutoSize
```  

Move user object
```powershell 
$userDN = (Get-ADUser -Identity (Read-Host "Username")).DistinguishedName
Move-ADObject -Identity $userDN -TargetPath (Read-Host "Path")

Move-ADObject -Identity "CN=Tony Hawk,OU=Users,OU=Ventura,DC=SK8ORDIE,DC=local" -TargetPath "OU=Disabled Users,DC=SK8ORDIE,DC=local"
```

Disable user  
```powershell
Disable-ADAccount -Identity (Read-Host "Username")
```

Enablee user  
```powershell
Enable-ADAccount -Identity (Read-Host "Username")
```

Unlock Account
```powershell
Unlock-ADAccount -Identity (Read-Host "Username")  
```

Set account password  
```powershell
Set-ADAccountPassword -Identity (Read-Host "Username") -NewPassword (Read-Host "Enter the new password" -AsSecureString)
```

Force password change at logon
```powershell
Set-ADUser -Identity (Read-Host "Username") -ChangePasswordAtLogon $true 
```

AD Sync  
```powershell
Start-ADSyncSyncCycle -PolicyType delta
```

Get workstations a user is allowed to logon to
```powershell
Get-ADUser -Filter { LogonWorkstations -ne "$null" } -Properties LogonWorkstations | Select-Object SamAccountName, LogonWorkstations
```

Set workstations user can logon to
```powershell
Set-ADUser -Identity "username" -LogonWorkstations "PC1,PC2"   
```

Create new domain user
```powershell
New-ADUser -Name "John Smith" -SamAccountName JSmith -UserPrincipalName "jsmith@contoso.loc" -GivenName "John" -Surname "Smith" -Enabled $true -AccountPassword (Read-Host "password" -AsSecureString) -Path "CN=Users,DC=contoso,DC=loc" -EmailAddress "jsmith@contoso.loc"
```

Get accounts trusted to authorize for delegation 
```powershell
Get-ADUser -Filter {UserAccountControl -band 16777216} -Properties TrustedToAuthForDelegation | Select-Object Name, SamAccountName, TrustedToAuthForDelegation
```
-or-
```powershell
Get-ADUser -Filter * -Properties TrustedToAuthForDelegation | Where-Object { $_.TrustedToAuthForDelegation -eq $true } | Select-Object Name, SamAccountName, TrustedToAuthForDelegation
```

Set the password to never expire
```powershell
Set-ADUser -Identity "UserName" -PasswordNeverExpires $true
```

### Get all enabled accounts
```powershell
Get-ADUser -Filter {Enabled -eq $true}
```

Get locked accounts
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

### Find Keroastable acounts
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