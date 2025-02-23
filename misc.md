
Find command (basic)  
```powershell  
Get-Command notepad.exe
```  

Find command (search entire drive)  
```powershell
Get-ChildItem -Path C:\ -Filter chrome.exe -Recurse -ErrorAction SilentlyContinue -Force
```  

Verify SMB v1.0 status
```powershell  
Get-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"
```

Enable SMB v1.0  
```powershell
Enable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol" -NoRestart
```

Get file hash
```powershell  
Get-FileHash -Path "C:\<path to>\some.exe" -Algorithm MD5
```

Get Startup programs   
```powershell
Get-CimInstance Win32_StartupCommand | Select-Object Name, Command, Location, User  
```

Add startup program  
```powershell  
New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "Application" -Value "C:\<path to>\some.exe"

New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce" -Name "Application" -Value "C:\<path to>\some.exe"
```  

0x0  
```bash
foo > file.txt && curl -F 'file=@file.txt' http://0x0.st
```  


Rename PC:
```powershell
$credential = New-Object System.Management.Automation.PSCredential((Read-Host "Domain admin username"), (Read-Host -Prompt "Domain admin password" -AsSecureString))

Rename-Computer -NewName (Read-Host "New Hostname") -DomainCredential $credential
$credential = $null
```

Invoke command on server
```powershell
Invoke-Command -ComputerName <Hostname> -Credential <Domain\user> 
```

Create PowerShell session on server
```powershell
Enter-PSSession -ComputerName <Hostname> -Credential <Domain\user>
```

Create new OU
```powershell
New-ADOrganizationalUnit -Name "SomeOU" -Path "CN=Users,DC=contoso,DC=loc"
```

Get all computer objects on domain
```powershell
Get-ADComputer -Filter *
```

Get All domain objects
```powershell
Get-ADObject -Filter *
```

Get all Organizational units
```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName
```

Find hosts running WinRM
```powershell
$baseIP = "x.x.x."
$startHostNumber = 1
$endHostNumber = 254

for ($i = $startHostNumber; $i -le $endHostNumber; $i++) {
    $computer = "$baseIP$i"
    if (Test-WSMan -ComputerName $computer -ErrorAction SilentlyContinue) {
        Write-Host "WinRM is running on $computer"
    } else {
        # Write-Host "WinRM is not running on $computer"
    }
}
```

Get local file ACL
```powershell
$FolderACL = Get-Acl -Path "C:\Windows\"
$FolderACL.Access | Format-Table IdentityReference, FileSystemRights
```