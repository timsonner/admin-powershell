### Export all firewall rules to a CSV file
```powershell
Get-NetFirewallRule | Select-Object -Property * | Export-Csv -Path (Read-Host "CSV path") -NoTypeInformation
```

### Export specific firewall rules to an XML file
```powershell
Get-NetFirewallRule -DisplayName (Read-Host "Rule name") | Export-Clixml -Path (Read-Host "XML path")
```

### Export all firewall rules to an XML file
```powershell
Export-WindowsFirewallRules -FilePath (Read-Host "XML path")
```
