### Query individual OU
```powershell
Get-ADOrganizationalUnit -Filter {Name -eq (Reah-Host "OU")}
```

### Query based on base OU
```powershell
Get-ADObject -Filter * -SearchBase "OU=SomeOrgUnit,DC=Contoso,DC=com"   
```

### Find disabled users OU
```powershell
Get-ADDomainController
Get-ADObject -Filter * -SearchBase "DC=corp,DC=foo,DC=com" | findstr -i disabled
```

### Get all computer objects on domain
```powershell
Get-ADComputer -Filter *
```

### Get All domain objects
```powershell
Get-ADObject -Filter *
```

### Get all Organizational units
```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

### Get current domain - Windows
```powershell
(Get-WmiObject Win32_ComputerSystem).Domain
```

### Enumerate computers as domain admin
```powershell
# Create a DirectoryEntry object
$directoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://<Domain Controller Goes Here>")

# Create a DirectorySearcher object
$searcher = New-Object System.DirectoryServices.DirectorySearcher($directoryEntry)

# Set the LDAP query filter
$searcher.Filter = "(objectClass=computer)"

# Execute the LDAP query
$results = $searcher.FindAll()

# Process the results
foreach ($result in $results) {
    $computer = $result.GetDirectoryEntry()

    # Access computer attributes
    $name = $computer.Name
    $dn = $computer.DistinguishedName

    # Do something with the computer information
    Write-Host "Computer Name: $name"
    Write-Host "Distinguished Name: $dn"
}

# Clean up the DirectorySearcher and DirectoryEntry objects
$searcher.Dispose()
$directoryEntry.Dispose()
```