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