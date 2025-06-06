### Add local user
```powershell
New-LocalUser -Name (Read-Host "Username") -Password (Read-Host "Password" -AsSecureString)
```

### Add local user to group  
```powershell
Add-LocalGroupMember -Group "Administrators" -Member "JohnDoe"
```

### Add AD group to local admins  
```powershell
Add-LocalGroupMember -Group "Administrators" -Member "<domain>\Administrators"
```

### Get account info by SID locally  
```powershell 
Get-WmiObject win32_useraccount | Select domain,name,sid | findstr -i "<SID goes here>"
```
### Add user to local group  
```powershell
Add-LocalGroupMember -Group (Read-Host "Group") -Member (Read-Host "Member")
```  

### Get members of local group  
```powershell
Get-LocalGroupMember -Group (Read-Host "Group")  
```  

### Get all local groups  
```powershell
Get-LocalGroup
```  

### Get group description  
```powershell
Get-LocalGroup -Group (Read-Host "Group")
```  

### Get group membership of local user  
```powershell
$locuser = (Read-Host "Username")
$hostname = $env:COMPUTERNAME
Get-LocalGroup | ForEach-Object {
    $groupName = $_.Name
    $groupDescription = $_.Description
    Get-LocalGroupMember -Group $groupName | Where-Object {
        $_.Name -eq "$hostname\$locuser"
    } | ForEach-Object {
        "--------------------------------------------------"
        [PSCustomObject]@{
            Group           = $groupName
            Description     = $groupDescription
            ObjectClass     = $_.ObjectClass
            Name            = $_.Name
            PrincipalSource = $_.PrincipalSource
        }
    }
}
```  

### Get SID and other info of local user
```powershell
Get-Localuser (Read-Host "Username") | Select-Object *
```  

### Create local user account
```powershell
New-LocalUser -Name (Read-Host "Username") -Password (Read-Host -AsSecureString "Password") -FullName (Read-Host "Full Name") -Description (Read-Host "User Description")
```  

### Get disabled local accounts
```powershell
Get-LocalUser | Where-Object { $_.Enabled -eq $false }
```  

### Set password for local user  
```powershell
Set-LocalUser -Name (Read-Host "Username") -Password (Read-Host -AsSecureString "Password")
```  

### Enable account of local user
```powershell
Enable-LocalUser -Name (Read-Host "Username")
```  

### Remove local user  
```powershell
$sid = (Get-CimInstance -ClassName Win32_UserAccount | Where-Object { $_.Name -eq "johnm" }).SID
reg delete "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$sid" /f
Remove-Item -Path "C:\Users\johns" -Recurse -Force
```

### Get SID based on username
```powershell
Get-CimInstance Win32_UserAccount -Filter Read-Host ("Username") | Select-Object Name, SID
```

### Get SID based on group name
```powershell
Get-CimInstance Win32_GroupAccount -Filter Read-Host ("Group") | Select-Object Name, SID
```

### Get all local users
```powershell
Get-LocalUser
```  

### Get local users (long way)
```powershell
$localUsers = Get-CimInstance Win32_UserAccount -Filter "LocalAccount='True'"

foreach ($user in $localUsers) {
    Write-Host "Username: $($user.Caption)"
    Write-Host "Full Name: $($user.FullName)"
    Write-Host "Description: $($user.Description)"
    Write-Host "SID: $($user.SID)"
    Write-Host "Disabled: $($user.Disabled)"
    Write-Host "Password Expires: $($user.PasswordExpires)"
    Write-Host "Last Logon: $($user.LastLogin)"
    Write-Host "----------------------------------------"
}
```

### Get local admins
```powershell
Get-LocalGroupMember Administrators | Select-Object Name, PrincipalSource | Where-Object { $_.PrincipalSource -eq "Local" }
```

### Get all members of the local Administrators group (long way)
```powershell
$adminGroup = [ADSI]"WinNT://./Administrators,group"
$adminMembers = @()
foreach ($member in $adminGroup.psbase.Invoke("Members")) {
   $user = $member.GetType().InvokeMember("Name", 'GetProperty', $null, $member, $null)
   $adminMembers += $user
}

#Get last logon info for each admin account
$adminInfo = foreach ($admin in $adminMembers) {
   try {
       $userAccount = Get-LocalUser -Name $admin -ErrorAction Stop
       [PSCustomObject]@{
           Username     = $userAccount.Name
           Enabled      = $userAccount.Enabled
           LastLogon    = $userAccount.LastLogon
           Description  = $userAccount.Description
       }
   } catch {
       Write-Warning "Could not retrieve info for $admin"
   }
}
# Export to CSV
$adminInfo | Export-Csv -Path "$env:USERPROFILE\AdminAccounts.csv" -NoTypeInformation
Write-Output "Admin account information exported to ~/AdminAccounts.csv"
```