Get printer info
```powershell
Get-Printer | Select-Object Name, PortName, DriverName
Get-Printer | Select-Object Name, PrinterStatus
```

List all configured printers, even if not currently connected
```powershell
Get-WmiObject -Class Win32_Printer | Select-Object Name, Network, Local, Shared, SystemName
```

Get driver info
```powershell
Get-PrinterDriver | Select-Object Name
Get-PrinterDriver -Name "Some printer driver" | Select-Object *
Get-PnpDevice -PresentOnly | Where-Object {$_.Class -eq 'Printer' -and $_.InstanceId -like '*USB*'}
```

Install printer driver
```powershell
Add-PrinterDriver -Name "Canon Generic Plus UFR II" 
```

Add printer
```powershell
Add-PrinterPort -Name (Read-Host "Printer port") -PrinterHostAddress (Read-Host "Printer IP")

Add-Printer -Name (Read-Host "Printer name") -PortName "(Read-Host "Printer port") -DriverName (Read-Host "Driver name")
```

Print 
```powershell
(Read-Host "Message to print") | Out-Printer -Name (Read-Host "Printer")
```

List shared printers
```powershell
Get-Printer | Where-Object { $_.Shared -eq $true } | Select-Object Name, ShareName, PrinterStatus
```

Get priner port of workstation
```powershell
Get-PrinterPort -ComputerName some.server.contoso.local
```

### Send output to printer
```powershell
"Test print" | Out-Printer -Name "HP ENVY Photo 7800 series"
```

### Clear print queue
```powershell
Get-Printer | ForEach-Object {
    $printerName = $_.Name
    Get-PrintJob -PrinterName $printerName | Remove-PrintJob
}
```