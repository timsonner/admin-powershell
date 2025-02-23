Email claw back
```powershell
Import-Module ExchangeOnlineManagement
Connect-IPPSSession
$Search = New-ComplianceSearch -Name "Remove-Evil-Email" -ExchangeLocation All -ContentMatchQuery Subject:"Not a phishing email"
Start-ComplianceSearch -Identity $Search.Identity
Get-ComplianceSearch -Identity $search.Name | FL Name,Items,Size,JobProgress,Status
New-ComplianceSearchAction -SearchName $search.Name -Purge -PurgeType HardDelete
Get-ComplianceSearchAction "${search.Name}_purge"

# Delete based on sender
# $search = New-ComplianceSearch -Name "SearchBySender" -ExchangeLocation All -ContentMatchQuery '(From:"dickbagspammer@gmail.com") AND (Subject:"INVOICE #4444")'
```

Expand mailbox size
```powershell
Import-Module ExchangeOnline
Connect-ExchangeOnline
Get-Mailbox -Identity "billyg@contoso.com" | Select-Object DisplayName, AutoExpandingArchiveEnabled
Enable-Mailbox -Identity "billyg@contoso.com" -AutoExpandingArchive
```

Get mailbox properties
```powershell
Get-Mailbox -Identity  user@null.null | Select-Object *
```

Get mailbox owners with 'Full Access' permissions
```powershell
Get-MailboxPermission -Identity (Read-Host "Mailbox") | Where-Object { $_.AccessRights -like "FullAccess" } | Select-Object -ExpandProperty User,Identity, AccessRights 
```

Get mailbox owners with 'Send As' permissions
```powershell
Get-RecipientPermission -Identity (Read-Host "Mailbox") | Select-Object -ExpandProperty Trustee 
```

Add Full Access permission
```powershell
Add-MailboxPermission -Identity (Read-Host "Mailbox") -User (Read-Host "User") -AccessRights FullAccess -InheritanceType All
```

Add Send As permission
```powershell
Add-RecipientPermission -Identity (Read-Host "Mailbox") -Trustee (Read-Host "Trustee") -AccessRights SendAs
```

Remove Full Access permission
```powershell
Remove-MailboxPermission -Identity (Read-Host "Mailbox") -User (Read-Host "User") -AccessRights FullAccess -InheritanceType All
```

Remove Send As permission
```powershell
Remove-RecipientPermission -Identity (Read-Host "Mailbox") -Trustee (Read-Host "Trustee") -AccessRights SendAs
```

Set forwarding address
```powershell
Set-Mailbox -Identity user@null.null -ForwardingSMTPAddress user201@null.null -DeliverToMailboxAndForward $true
```
Get forwardin address of mailbox
```powershell
Get-Mailbox  user@null.null | Select-Object ForwardingSMTPAddress
```

Get compliance journal rule
```powershell
Get-JournalRule
```
Remove compliance journal rule
```powershell
Remove-JournalRule
```

Set compliance journal rule
```powershell
Set-JournalRule -Identity "Apply to all messages" -Scope All
```

Get message trace
```powershell
Get-MessageTrace -SenderAddress "*@contoso.com" -StartDate "2025-12-25" -EndDate "2024-12-20" | Format-Table Timestamp, SenderAddress, RecipientAddress, Subject
Get-MessageTrace -SenderAddress "*@evildomain.com" -StartDate "2025-12-25" -EndDate "2024-12-20" | Export-Csv -Path "message_trace_results.csv" -NoTypeInformation
```