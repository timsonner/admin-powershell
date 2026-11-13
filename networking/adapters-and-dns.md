### Get status of IPv6 on network interfaces
```powershell
Get-NetAdapterBinding -ComponentID ms_tcpip6
```

### Disable IPv6 on a network interface
```powershell
Disable-NetAdapterBinding -Name (Read-Host "Adapter name") -ComponentID ms_tcpip6
```

### Get interface properties
```powershell
Get-NetAdapter | Select-Object -Property Name, InterfaceDescription, Status
```

### Get Network Connection Profile (get the index here)
```powershell
Get-NetConnectionProfile
```

### Set network connection profile to private
```powershell
$index = [int](Read-Host "Interface index")
Set-NetConnectionProfile -InterfaceIndex $index -NetworkCategory Private
```

### Set primary DNS
```powershell
$adapter = Read-Host "Adapter name"
$dns = Read-Host "DNS server"
Set-DnsClientServerAddress -InterfaceIndex (Get-NetAdapter -Name $adapter | Select-Object -ExpandProperty 'ifIndex') -ServerAddresses $dns
```

### Set primary and secondary DNS
```powershell
$index = [int](Read-Host "Interface index")
$primary = Read-Host "Primary DNS"
$secondary = Read-Host "Secondary DNS"
Get-NetAdapter
Set-DnsClientServerAddress -InterfaceIndex $index -ServerAddresses ($primary, $secondary)
```

### Resolve DMARC, DKIM, and SPF
```powershell
$domain = Read-Host "Domain"

# DMARC
Resolve-DnsName -Type TXT "_dmarc.$domain"

# SPF
Resolve-DnsName -Type TXT $domain | Where-Object { $_.Strings -match "v=spf1" }

# DKIM Selector1
Resolve-DnsName -Type TXT "selector1._domainkey.$domain"

# DKIM Selector2
Resolve-DnsName -Type TXT "selector2._domainkey.$domain"
```

### While loop every 5 min
```powershell
$domain = Read-Host "Domain"
while ($true) {
    Resolve-DnsName -Type TXT $domain | Where-Object { $_.Strings -match "v=spf1" }

    Start-Sleep -Seconds 300  # Wait 5 minutes
}
```

### Get SPF TXT record of domain
```powershell
$domain = Read-Host "Domain"
Resolve-DnsName -Name $domain -Type TXT
```

### Get DMARC TXT record of domain
```powershell
$domain = Read-Host "Domain"
Resolve-DnsName -Name "_dmarc.$domain" -Type TXT
```

### Get DKIM TXT record of domain
```powershell
$domain = Read-Host "Domain"
$selector = Read-Host "DKIM selector"
$dkimDomain = "$selector._domainkey.$domain"

try {
    $cnameRecord = Resolve-DnsName -Name $dkimDomain -Type CNAME -ErrorAction Stop
    if ($cnameRecord.NameHost) {
        # Resolve TXT record from CNAME target
        $txtRecord = Resolve-DnsName -Name $cnameRecord.NameHost -Type TXT -ErrorAction Stop
        Write-Output "TXT Record for ${dkimDomain} (via CNAME $($cnameRecord.NameHost)):"
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

### Make request and view site
```powershell
$response = Invoke-WebRequest -Uri (Read-Host "URI") -UseBasicParsing
$response.Content
```
