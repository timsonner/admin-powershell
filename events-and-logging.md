### Get Windows event
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4724} | 
  Where-Object {$_.Properties[0].Value -eq "<USERNAME>"} | 
  ForEach-Object { 
    $_ | Select-Object * 
  }
```

### Get security related events  
```powershell
Get-WinEvent -LogName Security -FilterXPath "*[System[(EventID=4728 or EventID=4729 or EventID=4732 or EventID=4733 or EventID=4756 or EventID=4757)]]" | Format-List -Property *
```

### Export event logs (.evtx format) to .txt  
```powershell
Get-WinEvent -Path "C:\temp\system.evtx" | Format-Table -AutoSize | Out-String -Width 4096 | Out-File "C:\temp\eventlog.txt"
```

### Get security event based on username  
```powershell
Get-ADObject -Filter { ObjectSID -eq (Read-Host "SID") } -Properties Name,ObjectClass

Get-ADComputer -Filter * | ForEach-Object {
    $computer = $_.Name
    $session = quser /server:$computer 2>$null | Where-Object { $_ -match (Read-Host "Username") }
    if ($session) {
        Write-Output "$computer - $session"
    }
}
```

### Get event corelated to username
```powershell
Get-EventLog -LogName Security -InstanceId 4624 | Where-Object { $_.Message -match (Read-Host "Username") } | Select-Object MachineName, TimeGenerated
```

### Get last logon of user
```powershell
Get-ADComputer -Filter * | ForEach-Object {
    Get-ADUser -Filter {SamAccountName -eq (Read-Host "Username")} -Properties LastLogon | 
    Select-Object Name, LastLogon
}
```

### Get last logon of user and timestamp
```powershell
Get-ADUser -Filter {SamAccountName -eq (Read-Host "Username")} -Properties LastLogon | 
Select-Object Name, @{Name="LastLogon"; Expression={[DateTime]::FromFileTime($_.LastLogon)}}
```

### Get events based on error leve *1 being most severe
```powershell
Get-WinEvent -FilterHashtable @{LogName='System'; Level=1,2,3} | Format-List
```

### Get event logs based on Event ID  e.g. 4740 - lockout
```powershell
Get-WinEvent -LogName "Application" -FilterXPath "*[System[EventID=1000]]" | Format-List TimeCreated, Message
```

### Get last 50 Error and Warning events
```powershell
Get-EventLog -LogName System -EntryType Error,Warning -Newest 50 | Format-List
Get-EventLog -LogName Application -EntryType Error,Warning -Newest 50 | Format-List
```

### Query Security log based on username and event ID
```powershell
$account = (Read-Host "username")
$events = Get-EventLog -LogName Security -InstanceId 4740 | 
    Where-Object { $_.Message -match $account }

$events | ForEach-Object {
    Write-Output "TimeGenerated: $($_.TimeGenerated)"
    Write-Output "Message:"
    Write-Output $($_.Message)
    Write-Output "--------------------------------------------------"
}
```

### Query Security log of DC for events based on username and event ID
```powershell
$account = Read-Host "Username"
$dcs = Get-ADDomainController -Filter * | Select-Object -ExpandProperty HostName

foreach ($dc in $dcs) {
    Write-Output "Checking failed logon attempts on $dc..."
    
    Invoke-Command -ComputerName $dc -ScriptBlock {
        param ($account)
        Get-WinEvent -LogName Security -FilterHashtable @{ID=4625} |
            Where-Object { $_.Message -match $account } |
            Select-Object TimeCreated,
                          @{Name="SourceIP"; Expression={($_.Properties[18].Value)}},
                          @{Name="WorkstationName"; Expression={($_.Properties[11].Value)}},
                          Message
    } -ArgumentList $account
}
```

### Search all logs for username
```powershell
$account = Read-Host "Username"
$logs = Get-WinEvent -ListLog *

foreach ($log in $logs) {
    Write-Output "Searching in log: $($log.LogName)..."
    try {
        Get-WinEvent -LogName $log.LogName -ErrorAction Stop |
            Where-Object { $_.Message -like "*$account*" } |
            Select-Object @{Name="LogName"; Expression={$log.LogName}}, TimeCreated, Message
    } catch {
        Write-Output "Unable to access log: $($log.LogName)"
    }
}
```

### Query the specific log (Security, etc.) for events matching the username
```powershell
$logName = "Security"
# $logName = "Quickpass Events"
$account = Read-Host "Username"

Write-Output "Searching in log: $logName..."

try {
    Get-WinEvent -LogName $logName |
        Where-Object { $_.Message -like "*$account*" } |
        ForEach-Object {
            [PSCustomObject]@{
                TimeCreated = $_.TimeCreated
                EventID     = $_.Id
                Provider    = $_.ProviderName
                Message     = $_.Message
                RecordID    = $_.RecordId
            }
        } | Format-Table -AutoSize
} catch {
    Write-Output "Unable to access log: $logName"
}
```

### Query DC for all events based on event ID
```powershell
$pdc = (Get-ADDomain).PDCEmulator
$filterHash = @{LogName = "Security"; Id = 4740; StartTime = (Get-Date).AddDays(-1)}
$lockoutEvents = Get-WinEvent -ComputerName $pdc -FilterHashTable $filterHash -ErrorAction SilentlyContinue
$lockoutEvents | Select @{Name = "LockedUser"; Expression = {$_.Properties[0].Value}}, `
                        @{Name = "SourceComputer"; Expression = {$_.Properties[1].Value}}, `
                        @{Name = "DomainController"; Expression = {$_.Properties[4].Value}}, TimeCreated

#  4771 (Kerberos Authentication) or event ID 4776 (NTLM authentication) before the event ID 4740 
```

### Get events by using an XPath
```powershell
# Possible events: 4625, 4740

# Specify the name or IP address of the remote computer
$remoteComputer = "x.x.x.x"

$filterXPath = @"
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[System[(EventID=4740)]]
    </Select>
  </Query>
</QueryList>
"@

$lockoutEvents = Get-WinEvent -LogName "Security" -ComputerName $remoteComputer -FilterXPath $filterXPath

foreach ($event in $lockoutEvents) {
    Write-Output "Event ID: $($event.Id)"
    Write-Output "Time Created: $($event.TimeCreated)"
    Write-Output "Message:"
    $event.Message
    Write-Output "-------------------------------------------------------"
}
```

### Get local logon events
```powershell
$LogonEvents = Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4624  
} -MaxEvents 10  

foreach ($event in $LogonEvents) {
    $event.Message
    Write-Host "----------------------------------------"
}
```