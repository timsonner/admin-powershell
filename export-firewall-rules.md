### Export all firewall rules to a CSV file
```powershell
Get-NetFirewallRule | Select-Object -Property * | Export-Csv -Path "C:\<path to>\FirewallRules.csv" -NoTypeInformation
```

### Export specific firewall rules to an XML file
```powershell
Get-NetFirewallRule -DisplayName "RuleName" | Export-Clixml -Path "C:\<path to>\FirewallRules.xml"
```

### Export all firewall rules to an XML file
```powershell
Export-WindowsFirewallRules -FilePath "C:\<path to>\FirewallRules.xml"
```