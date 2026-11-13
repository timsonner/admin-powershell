### Get all AD users and filter by PasswordLastSet date
```powershell
$Days = [int](Read-Host "Password age in days")
$thresholdDate = (Get-Date).AddDays(-$Days)

Get-ADUser -Filter * -Properties PasswordLastSet | Where-Object {
    $_.PasswordLastSet -lt $thresholdDate -or $_.PasswordLastSet -eq $null
} | Select-Object Name, SamAccountName, PasswordLastSet | Format-Table -AutoSize
```

### Find accounts with passwords older than the given number of days
```powershell
$Days = [int](Read-Host "Password age in days")
$DateThreshold = (Get-Date).AddDays(-$Days)
Get-ADUser -Filter * -Properties SamAccountName, LastLogonDate, PasswordLastSet, Enabled |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object SamAccountName, PasswordLastSet |
    Sort-Object PasswordLastSet
```

### Find enabled accounts with passwords older than the given number of days
```powershell
$Days = [int](Read-Host "Password age in days")
$DateThreshold = (Get-Date).AddDays(-$Days)
Get-ADUser -Filter * -Properties SamAccountName, LastLogonDate, PasswordLastSet, Enabled |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object SamAccountName, PasswordLastSet |
    Sort-Object PasswordLastSet
```

### Find non-service accounts with passwords older than the given number of days
```powershell
$Days = [int](Read-Host "Password age in days")
$DateThreshold = (Get-Date).AddDays(-$Days)
Get-ADUser -Filter * -Properties SamAccountName, PasswordLastSet, Enabled, PasswordNeverExpires |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordNeverExpires -eq $false -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object SamAccountName, PasswordLastSet, PasswordNeverExpires |
    Sort-Object PasswordLastSet
```

### Find users that have not reset their password
```powershell
$users = Get-ADUser -Filter * -Properties SamAccountName, pwdLastSet, Enabled, PasswordNeverExpires |
    Where-Object {
        $_.Enabled -eq $true -and
        $_.PasswordNeverExpires -eq $false -and
        $_.pwdLastSet -eq 0
    } |
    Select-Object -ExpandProperty SamAccountName |
    Sort-Object
```

### Create array of usernames based on search criteria
```powershell
$Days = [int](Read-Host "Password age in days")
$DateThreshold = (Get-Date).AddDays(-$Days)

# change this logic for different searches:
$users = Get-ADUser -Filter * -Properties SamAccountName, PasswordLastSet, Enabled, PasswordNeverExpires |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordNeverExpires -eq $false -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object -ExpandProperty SamAccountName |
    Sort-Object

$usersList = $users | ForEach-Object { "`"$_`"" }

$usersListString = "`$users = @(" + [String]::Join(",`r`n", $usersList) + ")"

$usersListString
```

### Force reset on a list of usernames
```powershell
$users = (Read-Host "Usernames (comma separated)") -split ',' | ForEach-Object { $_.Trim() } | Where-Object { $_ }

foreach ($user in $users) {
    try {
        Set-ADUser -Identity $user -ChangePasswordAtLogon $true
        Write-Host "Password reset enforced for user: $user" -ForegroundColor Green
    } catch {
        Write-Host "Error updating user: $user - $_" -ForegroundColor Red
    }
}
```
