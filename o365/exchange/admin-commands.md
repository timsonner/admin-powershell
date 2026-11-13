### Import ExchangeOnlineManagement module
```powershell
Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser
Import-Module ExchangeOnlineManagement
Connect-ExchangeOnline
Connect-IPPSSession
Get-Command -Module ExchangeOnlineManagement
```

### Test Exchange credentials
```powershell
Connect-ExchangeOnline -UserPrincipalName (Read-Host "User")
Disconnect-ExchangeOnline
```

### Get mailbox info
```powershell
Get-Mailbox -Identity (Read-Host "User")
Get-MailboxStatistics -Identity (Read-Host "User")
```

### Distribution groups
```powershell
Get-DistributionGroupMember -Identity (Read-Host "Distribution Group") | Select-Object DisplayName, PrimarySmtpAddress
Get-DistributionGroup -Identity (Read-Host "Group name") | Select-Object DisplayName, ManagedBy
Set-DistributionGroup -Identity (Read-Host "Group name") -ManagedBy (Read-Host "New owner email address")
```

### Email claw back
```powershell
Import-Module ExchangeOnlineManagement
Connect-IPPSSession
$searchName = Read-Host "Search name"
$subject = Read-Host "Subject"
$search = New-ComplianceSearch -Name $searchName -ExchangeLocation All -ContentMatchQuery "Subject:`"$subject`""
Start-ComplianceSearch -Identity $search.Identity
Get-ComplianceSearch -Identity $search.Name | Format-List Name, Items, Size, JobProgress, Status
New-ComplianceSearchAction -SearchName $search.Name -Purge -PurgeType HardDelete
Get-ComplianceSearchAction ($search.Name + "_Purge")
```

### Expand mailbox size
```powershell
Import-Module ExchangeOnlineManagement
Connect-ExchangeOnline
$mailbox = Read-Host "Mailbox"
Get-Mailbox -Identity $mailbox | Select-Object DisplayName, AutoExpandingArchiveEnabled
Enable-Mailbox -Identity $mailbox -AutoExpandingArchive
```

### Get mailbox properties
```powershell
Get-Mailbox -Identity (Read-Host "Mailbox") | Select-Object *
```

### Get mailbox owners with Full Access permissions
```powershell
Get-MailboxPermission -Identity (Read-Host "Mailbox") | Where-Object { $_.AccessRights -like "FullAccess" } | Select-Object User, Identity, AccessRights
```

### Get mailbox owners with Send As permissions
```powershell
Get-RecipientPermission -Identity (Read-Host "Mailbox") | Select-Object -ExpandProperty Trustee
```

### Add Full Access permission
```powershell
Add-MailboxPermission -Identity (Read-Host "Mailbox") -User (Read-Host "User") -AccessRights FullAccess -InheritanceType All
```

### Get Full Access perms of mailbox
```powershell
Get-MailboxPermission -Identity (Read-Host "Mailbox") | Where-Object { $_.AccessRights -like "FullAccess" } | Select-Object User, Identity, AccessRights
```

### Add calendar permissions
```powershell
$mailbox = Read-Host "Mailbox"
Add-MailboxFolderPermission -Identity "${mailbox}:\Calendar" -User (Read-Host "Owner") -AccessRights Owner
Add-MailboxFolderPermission -Identity "${mailbox}:\Calendar" -User (Read-Host "Reviewer") -AccessRights Reviewer
```

### Add Send As permission
```powershell
Add-RecipientPermission -Identity (Read-Host "Mailbox") -Trustee (Read-Host "Trustee") -AccessRights SendAs
```

### Remove Full Access permission
```powershell
Remove-MailboxPermission -Identity (Read-Host "Mailbox") -User (Read-Host "User") -AccessRights FullAccess -InheritanceType All
```

### Remove Send As permission
```powershell
Remove-RecipientPermission -Identity (Read-Host "Mailbox") -Trustee (Read-Host "Trustee") -AccessRights SendAs
```

### Set forwarding address
```powershell
Set-Mailbox -Identity (Read-Host "Mailbox") -ForwardingSMTPAddress (Read-Host "Forwarding address") -DeliverToMailboxAndForward $true
```

### Get forwarding address of mailbox
```powershell
Get-Mailbox -Identity (Read-Host "Mailbox") | Select-Object ForwardingSMTPAddress
```

### Get compliance journal rule
```powershell
Get-JournalRule
```

### Remove compliance journal rule
```powershell
Remove-JournalRule -Identity (Read-Host "Journal rule")
```

### Set compliance journal rule
```powershell
Set-JournalRule -Identity (Read-Host "Journal rule") -Scope All
```

### Get message trace
```powershell
$start = Read-Host "Start date"
$end = Read-Host "End date"
Get-MessageTrace -SenderAddress (Read-Host "Sender address") -StartDate $start -EndDate $end | Format-Table Timestamp, SenderAddress, RecipientAddress, Subject
Get-MessageTrace -SenderAddress (Read-Host "Sender address for CSV") -StartDate $start -EndDate $end | Export-Csv -Path (Read-Host "CSV path") -NoTypeInformation
```
