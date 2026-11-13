### List all groups
```powershell
Get-ADGroup -Filter *
```

### Find a specific group by name
```powershell
$name = Read-Host "Group"
Get-ADGroup -Filter "Name -like '$name'"
```

### Get information about a specific group
```powershell
Get-ADGroup -Identity (Read-Host "Group") | Format-List
```

### Get members of specific group
```powershell
Get-ADGroupMember -Identity (Read-Host "Group") -Recursive | Select-Object Name, SamAccountName
```

### Get members of a group
```powershell
Get-ADGroupMember -Identity (Read-Host "Group")
```

### List all groups in a specific Organizational Unit (OU)
```powershell
Get-ADGroup -Filter * -SearchBase (Read-Host "Search base")
```

### Find groups with specific properties
```powershell
Get-ADGroup -Filter { GroupCategory -eq "Security" -and GroupScope -eq "Global" }
```

### Find user in group
```powershell
$group = Read-Host "Group"
$user = Read-Host "Username"
Get-ADGroupMember -Identity $group | Where-Object { $_.DistinguishedName -like "*$user*" }
```

### Add user to a group
```powershell
Add-ADGroupMember -Identity (Read-Host "Group") -Members (Read-Host "Username")
```

### Add user to multiple groups
```powershell
$member = Read-Host "Username"
$GroupNames = "Group Policy Creator Owners", "Domain Admins", "Enterprise Admins", "Schema Admins", "Administrators"
foreach ($GroupName in $GroupNames) {
    Add-ADGroupMember -Identity $GroupName -Members $member
}
```

### Remove user from group
```powershell
Remove-ADGroupMember -Identity (Read-Host "Group") -Members (Read-Host "Username")
```

### Remove AD user from all groups
```powershell
$UserName = Read-Host "Username"
$UserGroups = Get-ADPrincipalGroupMembership -Identity $UserName

foreach ($Group in $UserGroups) {
    Remove-ADGroupMember -Identity $Group -Members $UserName -Confirm:$false
}
```

### Get group membership of a user
```powershell
Get-ADUser -Identity (Read-Host "Username") -Properties MemberOf | Select-Object -ExpandProperty MemberOf
```

### Get group access control permissions
```powershell
$groupDN = Read-Host "Group LDAP path"
$directoryEntry = New-Object System.DirectoryServices.DirectoryEntry($groupDN)
$acl = $directoryEntry.ObjectSecurity.Access
$acl | Select-Object IdentityReference, AccessControlType, ActiveDirectoryRights
```
