Get status of IPv6 on network interfaces
```powershell
Get-NetAdapterBinding -ComponentID ms_tcpip6
```

Disable ethernet on specific network interface
```powershell
Disable-NetAdapterBinding -Name "Ethernet 3" -ComponentID ms_tcpip6
```

Get interface properties
```powershell
Get-NetAdapter | Select-Object -Property Name, InterfaceDescription, Status
```

Get Network Connection Profile (get the index here)  
```powershell
Get-NetConnectionProfile
```

Set network connection profile to private  
```powershell
Set-NetConnectionProfile -InterfaceIndex <Index goes here> -NetworkCategory Private
```

Set primary DNS
```powershell
Set-DnsClientServerAddress -InterfaceIndex (Get-NetAdapter -Name 'Ethernet' | Select-Object -ExpandProperty 'ifIndex') -ServerAddresses 10.200.35.101
```

Set primary and secondary DNS
```powershell
 Get-NetAdapter  
 Set-DnsClientServerAddress -InterfaceIndex <Interface Number> -ServerAddresses ("1.1.1.1","9.9.9.9")  
 ```