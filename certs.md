### Get signer cert
```powershell
Get-AuthenticodeSignature -FilePath .\explorer.exe
```

### Export cert from binary
```powershell
$filePath = (Read-Host "File Path")
$certificate = Get-AuthenticodeSignature -FilePath $filePath | Select-Object -ExpandProperty SignerCertificate
$certificate | Export-Certificate -FilePath (Read-Host "Output Path (.cer)")
```

### Display cert info
```powershell
$exportedCertPath = (Read-Host "Certificate (.cer) Path")
$exportedCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$exportedCert.Import($exportedCertPath)
$exportedCert | Format-List -Property *
```

### Trust all certs (temporary)
```powershell
Add-Type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```