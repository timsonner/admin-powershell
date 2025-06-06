### nslookup
```
nslookup -q=txt selector1._domainkey.<domain>.com
nslookup -q=txt _dmarc.<domain>.com
```

### nslookup automated
```
@echo off;
set /p domain="Enter the domain name: ";
echo Running NSLOOKUP for A records;
nslookup -q=A %domain%;
echo.;
echo Running NSLOOKUP for MX records;
nslookup -q=MX %domain%;
echo.;
echo Running NSLOOKUP for NS records;
nslookup -q=NS %domain%;
echo.;
echo Running NSLOOKUP for TXT records;
nslookup -q=TXT %domain%;
echo.;
echo Running NSLOOKUP for SOA records;
nslookup -q=SOA %domain%;
echo.;
```
### Get connected Wi-Fi network:  
```
(netsh wlan show interfaces) -match '^\s+SSID' | ForEach-Object { $_.Trim() }
```

### Get wWi-Fi profiles  
```  
netsh wlan show profiles
```  
### Get info about Wi-Fi profile  
```  
netsh wlan show profile name="WiFiNetworkName"
```  
### Set Wi-Fi profile to connect automatically  
```  
netsh wlan set profileparameter name="WiFiNetworkName" connectionmode=auto
```  
### Get Wi-Fi interfaces
```  
netsh wlan show interfaces
```  
### Get Wi-Fi profiles
```
netsh wlan show profiles
```
### Get profile order of importance
```
netsh wlan set profileorder name="XXXXXX" interface="Wi-Fi" priority=1
```

### Delete Wi-Fi profile
```
netsh wlan delete profile name="XXXXXX"
```

### Activate account using net
```
net user <user> /domain /active:yes
```

### Remove groups using net  
```
net group "GroupName" username /delete /domain
```

### Add local user to local admin group
```
net localgroup Administrators domain\username /add
```

### System File checker  
```  
sfc /scannow
```  

### Remove Avira AV
```
/failfast:on /node:@"c:\temp\hosts.txt" product where name="Cyber Protect Agent" call uninstall /nointeractive
```

### Fix glitched Outlook UI  
```
outlook.exe /resetnavpane
```

### Reset Outlook folders
```  
outlook.exe /resetfoldernames
```  

### Get hard drive info  
```
wmic diskdrive get model,mediaType
```

### Export specific event logs  
```powershell
wevtutil epl System C:\path\to\output\system.evtx
```

### RDP from command
```
mstsc /v:x.x.x.x
```

### Credential injection
```
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

### DISM
```  
DISM /Online /Cleanup-Image /CheckHealth
```  

```
DISM /Online /Cleanup-Image /RestoreHealth
```

```
DISM /Get-WimInfo /WimFile:C:\temp\install.wim
```

Make dir and mount DISM image
```
mkdir C:\DISMSource
DISM /Mount-Wim /WimFile:<path/to/>install.wim /Index:1 /MountDir:<mount path>
```

Restore from image
```
DISM /Online /Cleanup-Image /RestoreHealth /Source:C:\DISMSource\Windows\WinSxS /LimitAccess
```

```
DISM /Unmount-Wim /MountDir:C:\DISMSource /Discard
```