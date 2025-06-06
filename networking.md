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

 ### Resolve DMARC, DKIM, and SPF
```powershell
# DMARC
Resolve-DnsName -Type TXT _dmarc.contoso.com

# SPF
Resolve-DnsName -Type TXT contoso.com | Where-Object { $_.Strings -match "v=spf1" }

# DKIM Selector1
Resolve-DnsName -Type TXT selector1._domainkey.contoso.com

# DKIM Selector2
Resolve-DnsName -Type TXT selector1._domainkey.contoso.com
```

### While loop every 5 min
```powershell
while ($true) {
    Resolve-DnsName -Type TXT contoso.com | Where-Object { $_.Strings -match "v=spf1" }

    Start-Sleep -Seconds 300  # Wait 5 minutes
}
```

Get SPF TXT record of domain
```powershell
$domain = Read-Host "Domain"
Resolve-DnsName -Name $domain -Type TXT
```

Get DMARC TXT record of domain
```powershell
$domain = Read-Host "Domain"
Resolve-DnsName -Name "_dmarc.$domain" -Type TXT
```

Get DKIM TXT record of domain
```powershell
$domain = Read-Host "Domain"
$dkimDomain = "selector1._domainkey.$domain"

try {
    $cnameRecord = Resolve-DnsName -Name $dkimDomain -Type CNAME -ErrorAction Stop
    if ($cnameRecord.NameHost) {
        # Resolve TXT record from CNAME target
        $txtRecord = Resolve-DnsName -Name $cnameRecord.NameHost -Type TXT -ErrorAction Stop
        Write-Output "TXT Record for ${dkimDomain} (via CNAME ${cnameRecord.NameHost}):"
        $txtRecord.Strings
    } else {
        Write-Output "No CNAME found. Resolving TXT record directly."
        $txtRecord = Resolve-DnsName -Name $dkimDomain -Type TXT -ErrorAction Stop
        Write-Output "TXT Record for ${dkimDomain}:"
        $txtRecord.Strings
    }
} catch {
    Write-Output "Failed to resolve DKIM record for ${dkimDomain}."
    Write-Output $_
}
```