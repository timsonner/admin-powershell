### Get mapped drives of user
```powershell
$UserSID = Read-Host "User SID"

$MappedDrivesKey = "Registry::HKEY_USERS\$UserSID\Network"

if (Test-Path $MappedDrivesKey) {
    Get-ChildItem -Path $MappedDrivesKey | ForEach-Object {
        [PSCustomObject]@{
            DriveLetter = $_.PSChildName
            RemotePath  = (Get-ItemProperty -Path $_.PSPath).RemotePath
            UserName    = (Get-ItemProperty -Path $_.PSPath).UserName
        }
    } | Format-Table -AutoSize
} else {
    Write-Host "No mapped drives found for SID $UserSID"
}
```
