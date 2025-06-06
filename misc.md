### Open PowerShell as Administrator
```powershell
Start-Process -Verb RunAs
```

### Temporary ExecutionPolicy bypass  
```powershell
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process
```

### Load local Dll  
```powershell
$currentDir = Get-Location
$bytes = [IO.File]::ReadAllBytes("$currentDir\payload.dll")
$assembly = [System.Reflection.Assembly]::Load($bytes)
$instance = $assembly.CreateInstance("ProgramNamespace.ProgramNamesspaceClass")
$instance.RunO()
```

### Spawn process from PsExec
```powershell
Start-Process -FilePath "powershell" -ArgumentList "-WindowStyle Hidden -Command Start-Process -NoNewWindow 'c:\tools\pstools\psexec64.exe' '-i -s -accepteula $(Read-Host "Elevated")'"                                                                                                                          
```

### Download Edge (Windows Server)  
```powershell
Invoke-WebRequest -Uri "https://c2rsetup.officeapps.live.com/c2r/downloadEdge.aspx?platform=Default&source=EdgeStablePage&Channel=Stable&language=en&brand=M100"
```

### Download SPICE Guest Tools
```powershell  
Invoke-WebRequest -Uri "https://spice-space.org/download/windows/spice-guest-tools/spice-guest-tools-latest.exe"
```

### Get cmdlets available from Module  
```powershell
Get-Command -Module ActiveDirectory | findstr -i group
```

### Get help with specific command
```powershell  
Get-Help Add-ADGroupMember
```
### Set domain password policy
```powershell
Set-ADDefaultDomainPasswordPolicy -identity contoso.local -MinPasswordAge 0
```

### Get processes using most memory
```powershell
Get-Process | Sort-Object -Descending WS | Select-Object -First 10 | Format-Table -Property Name, WS, ID
```

### Find command (basic)  
```powershell  
Get-Command notepad.exe
```  

### Find command (search entire drive)  
```powershell
Get-ChildItem -Path C:\ -Filter chrome.exe -Recurse -ErrorAction SilentlyContinue -Force
```  

### Verify SMB v1.0 status
```powershell  
Get-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"
```

### Enable SMB v1.0  
```powershell
Enable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol" -NoRestart
```

### Get file hash
```powershell  
Get-FileHash -Path "C:\<path to>\some.exe" -Algorithm MD5
```

### Get Startup programs   
```powershell
Get-CimInstance Win32_StartupCommand | Select-Object Name, Command, Location, User  
```

### Add startup program  
```powershell  
New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "Application" -Value "C:\<path to>\some.exe"

New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce" -Name "Application" -Value "C:\<path to>\some.exe"
```  

### 0x0.st - Linux version
```bash
foo > file.txt && curl -F 'file=@file.txt' http://0x0.st
```  

### Rename PC:
```powershell
$credential = New-Object System.Management.Automation.PSCredential((Read-Host "Domain admin username"), (Read-Host -Prompt "Domain admin password" -AsSecureString))

Rename-Computer -NewName (Read-Host "New Hostname") -DomainCredential $credential
$credential = $null
```

### Invoke command on server
```powershell
Invoke-Command -ComputerName <Hostname> -Credential <Domain\user> 
```

### Create PowerShell session on server
```powershell
Enter-PSSession -ComputerName <Hostname> -Credential <Domain\user>
```

### Create new OU
```powershell
New-ADOrganizationalUnit -Name "SomeOU" -Path "CN=Users,DC=contoso,DC=loc"
```

### Get local file ACL
```powershell
$FolderACL = Get-Acl -Path "C:\Windows\"
$FolderACL.Access | Format-Table IdentityReference, FileSystemRights
```

### Join workstation to domain
```powershell
 Add-Computer -DomainName <domain name>
 ```

### Windows key from Registry - Windows 11
```powershell
    Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ClipSVC\"
```

### Windows key from Registry - Windows 10  
 ```powershell
    Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform"
```

### Get location of process
```powershell
Get-Process -Id 11076 | Select-Object -ExpandProperty Path
```

### AD Sync  
```powershell
Start-ADSyncSyncCycle -PolicyType delta
```