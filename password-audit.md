### Get all AD users and filter by PasswordLastSet date
```powershell
$Days = 90
$thresholdDate = (Get-Date).AddDays(-$Days)

Get-ADUser -Filter * -Properties PasswordLastSet | Where-Object {
    $_.PasswordLastSet -lt $thresholdDate -or $_.PasswordLastSet -eq $null
} | Select-Object Name, SamAccountName, PasswordLastSet | Format-Table -AutoSize
```

### Find accounts with passwords older than 90 days
```powershell
$Days = 90
$DateThreshold = (Get-Date).AddDays(-$Days)
Get-ADUser -Filter * -Properties SamAccountName, LastLogonDate, PasswordLastSet, Enabled |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object SamAccountName, PasswordLastSet |
    Sort-Object PasswordLastSet
```

### Find account with default creds. Find accounts with passwords older than 90 days where created date and last set date are the same
```powershell
$Days = 90
$DateThreshold = (Get-Date).AddDays(-$Days)
Get-ADUser -Filter * -Properties SamAccountName, LastLogonDate, PasswordLastSet, Enabled |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object SamAccountName, PasswordLastSet |
    Sort-Object PasswordLastSet

# Name                    SamAccountName       Created                PasswordLastSet        
# ----                    --------------       -------                ---------------
```

### Find non-service accounts with creds older than 90 days
```powershell
$Days = 90
$DateThreshold = (Get-Date).AddDays(-$Days)
Get-ADUser -Filter * -Properties SamAccountName, PasswordLastSet, Enabled, PasswordNeverExpires |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordNeverExpires -eq $false -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object SamAccountName, PasswordLastSet, PasswordNeverExpires |
    Sort-Object PasswordLastSet

# SamAccountName       PasswordLastSet       PasswordNeverExpires 
# --------------       ---------------       --------------------
```

### Find users that havent reset password
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
$Days = 90
$DateThreshold = (Get-Date).AddDays(-$Days)

# change this logic for different searches:
$users = Get-ADUser -Filter * -Properties SamAccountName, PasswordLastSet, Enabled, PasswordNeverExpires |
    Where-Object { $_.Enabled -eq $true -and $_.PasswordNeverExpires -eq $false -and $_.PasswordLastSet -lt $DateThreshold } |
    Select-Object -ExpandProperty SamAccountName |
    Sort-Object

$usersList = $users | ForEach-Object { "`"$_`"" }

$usersListString = "users = @(" + [String]::Join(",`r`n", $usersList) + ")"

$usersListString

# $usersListString example: @("user1","user2")
```

### Force reset on array of usernames
```powershell
users = @(
"user1",
"user2",
"user3"
)

foreach ($user in $users) {
    try {
        Set-ADUser -Identity $user -ChangePasswordAtLogon $true
        Write-Host "Password reset enforced for user: $user" -ForegroundColor Green
    } catch {
        Write-Host "Error updating user: $user - $_" -ForegroundColor Red
    }
}
```