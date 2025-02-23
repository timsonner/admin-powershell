```powershell
$UserSID = "CHANGE_ME"

$MappedDrivesKey = "HKCU:\$UserSID\Network"

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

