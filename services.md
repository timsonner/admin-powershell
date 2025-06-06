### Get running services (dell)
```powershell
Get-Service | Where-Object { $_.Status -eq 'Running' } | Select-Object Status, Name, DisplayName | findstr -i dell
```

### Disable service
```powershell
Set-Service -Name "Spooler" -StartupType Disabled
```

### Disable multiple services
```powershell

#     "DellClientManagementService",

$services = @(
    "DDVCollectorSvcApi",
    "DDVDataCollector",
    "DDVRulesProcessor",
    "Dell SupportAssist Remediation",
    "DellDigitalDelivery",
    "DellTechHub",
    "SupportAssistAgent"
)

foreach ($svc in $services) {
    Set-Service -Name $svc -StartupType Disabled
    #Stop-Service $svc -Force
}
```

### Get services of local host
```powershell
Get-Service | Select-Object Name, DisplayName, Status, StartType, ServiceType | Export-Csv -Path ".\services.csv" -NoTypeInformation
Get-Service | Select-Object Name, DisplayName, Status, StartType, ServiceType | ConvertTo-Json | Out-File ".\services.json"
Get-Service | Select-Object Name, DisplayName, Status, StartType, ServiceType | Export-Clixml -Path ".\services.xml"
Get-Service | Select-Object Name, DisplayName, Status, StartType, ServiceType | ConvertTo-Html | Out-File ".\services.html"
```

### Get services of remote host... must have WinRM protocol enabled.
```powershell
$credential = Get-Credential
Invoke-Command -ComputerName <HostnameGoesHere> -Credential $credential -ScriptBlock { Get-Service } | Select-Object Name, DisplayName, Status, StartType, ServiceType | Export-Csv -Path ".\services.csv" -NoTypeInformation
```