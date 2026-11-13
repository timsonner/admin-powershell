### nslookup
```bat
set /p domain=Domain: 
nslookup -q=txt selector1._domainkey.%domain%
nslookup -q=txt _dmarc.%domain%
```

### nslookup automated
```bat
@echo off
set /p domain=Enter the domain name: 
echo Running NSLOOKUP for A records
nslookup -q=A %domain%
echo.
echo Running NSLOOKUP for MX records
nslookup -q=MX %domain%
echo.
echo Running NSLOOKUP for NS records
nslookup -q=NS %domain%
echo.
echo Running NSLOOKUP for TXT records
nslookup -q=TXT %domain%
echo.
echo Running NSLOOKUP for SOA records
nslookup -q=SOA %domain%
echo.
```

### Get connected Wi-Fi network
```powershell
(netsh wlan show interfaces) -match '^\s+SSID' | ForEach-Object { $_.Trim() }
```

### Get Wi-Fi profiles
```bat
netsh wlan show profiles
```

### Get info about Wi-Fi profile
```bat
set /p name=Wi-Fi profile: 
netsh wlan show profile name="%name%"
```

### Set Wi-Fi profile to connect automatically
```bat
set /p name=Wi-Fi profile: 
netsh wlan set profileparameter name="%name%" connectionmode=auto
```

### Get Wi-Fi interfaces
```bat
netsh wlan show interfaces
```

### Get Wi-Fi profiles (repeat)
```bat
netsh wlan show profiles
```

### Get profile order of importance
```bat
set /p name=Wi-Fi profile: 
set /p interface=Interface: 
netsh wlan set profileorder name="%name%" interface="%interface%" priority=1
```

### Delete Wi-Fi profile
```bat
set /p name=Wi-Fi profile: 
netsh wlan delete profile name="%name%"
```

### Activate account using net
```bat
set /p user=User: 
net user %user% /domain /active:yes
```

### Remove groups using net
```bat
set /p group=Group: 
set /p user=User: 
net group "%group%" %user% /delete /domain
```

### Add local user to local admin group
```bat
set /p member=Member: 
net localgroup Administrators %member% /add
```

### System File checker
```bat
sfc /scannow
```

### Remove a product with wmic
```bat
set /p hosts=Host list file: 
set /p product=Product name: 
wmic /failfast:on /node:@"%hosts%" product where name="%product%" call uninstall /nointeractive
```

### Fix glitched Outlook UI
```bat
outlook.exe /resetnavpane
```

### Reset Outlook folders
```bat
outlook.exe /resetfoldernames
```

### Get hard drive info
```bat
wmic diskdrive get model,mediaType
```

### Export specific event logs
```powershell
wevtutil epl System (Read-Host "Output path")
```

### RDP from command
```bat
set /p target=Computer: 
mstsc /v:%target%
```

### Credential injection
```bat
set /p account=Domain\username: 
runas.exe /netonly /user:%account% cmd.exe
```

### DISM
```bat
DISM /Online /Cleanup-Image /CheckHealth
```

```bat
DISM /Online /Cleanup-Image /RestoreHealth
```

```bat
set /p wim=WIM path: 
DISM /Get-WimInfo /WimFile:%wim%
```

### Make dir and mount DISM image
```bat
set /p mount=Mount path: 
set /p wim=WIM path: 
mkdir %mount%
DISM /Mount-Wim /WimFile:%wim% /Index:1 /MountDir:%mount%
```

### Restore from image
```bat
set /p source=Source path: 
DISM /Online /Cleanup-Image /RestoreHealth /Source:%source% /LimitAccess
```

```bat
set /p mount=Mount path: 
DISM /Unmount-Wim /MountDir:%mount% /Discard
```
