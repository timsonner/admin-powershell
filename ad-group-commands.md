List all groups
```powershell
Get-ADGroup -Filter *
```

Find a specific group by name
```powershell
Get-ADGroup -Filter { Name -like "GroupName" }
```

Get detailed information about a specific group
```powershell
Get-ADGroup -Identity "GroupName" | Format-List
```

List all groups in a specific Organizational Unit (OU)
```powershell
Get-ADGroup -Filter * -SearchBase "OU=Groups,DC=example,DC=com"
```

Find groups with specific properties
```powershell
Get-ADGroup -Filter { GroupCategory -eq "Security" -and GroupScope -eq "Global" }
```

Find user in group
```powershell
Get-ADGroupMember -Identity "<group name>" | Where-Object { $_.DistinguishedName -like "*<user name>*" }
```

Add user to a group  
```powershell
Add-ADGroupMember -Identity (Read-Host "Group") -Members (Read-Host "Username")
```

Add user to multiple groups
```powershell
$GroupNames = "Group Policy Creator Owners", "Domain Admins", "Enterprise Admins", "Schema Admins", "Administrators"
foreach ($GroupName in $GroupNames) {
    Add-ADGroupMember -Identity $GroupName -Members (Read-Host "Username")
}
```

Remove user from group
```powershell
Remove-ADGroupMember -Identity "GroupName" -Members "UserName"
```

Remove user from multiple groups
```powershell
$GroupNames = "Group Policy Creator Owners", "Domain Admins", "Enterprise Admins", "Schema Admins", "Administrators"
foreach ($GroupName in $GroupNames) {
    Remove-ADGroupMember -Identity $GroupName -Members (Read-Host "Username")
}
```

Remove user from all groups
```powershell
$UserGroups = Get-ADPrincipalGroupMembership -Identity (Read-Host "Username")
foreach ($Group in $UserGroups) {
    Remove-ADGroupMember -Identity $Group -Members $UserName -Confirm:$false
}
```

Get group membership of a user
```powershell
Get-ADUser -Identity (Read-Host "Username") -Properties MemberOf | Select-Object -ExpandProperty MemberOf
```  

Get group access control permissions  
```powershell  
$groupDN = "LDAP://CN=ContosoOwners,OU=Distribution Groups,OU=MyBusiness,DC=ContosoDomain,DC=local"
$directoryEntry = New-Object System.DirectoryServices.DirectoryEntry($groupDN)
$acl = $directoryEntry.ObjectSecurity.Access
$acl | Select-Object IdentityReference, AccessControlType, ActiveDirectoryRights
```  
