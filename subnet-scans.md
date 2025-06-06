### Scan for web servers
```powershell
1..254 | ForEach-Object {
    $ip = "192.168.10.$_"
    try {
        $response = Invoke-WebRequest -Uri "https://$ip" -UseBasicParsing -TimeoutSec 5
        if ($response.StatusCode -eq 200) {
            Write-Host "$ip"
        } else {
            Write-Host "$ip returned status $($response.StatusCode)"
        }
    } catch {}
}
```

### Check for open port
```powershell
$port=$port=80
1..254 | ForEach-Object {
    $ip = "192.168.3.$_"
    try {
        $tcp = Test-NetConnection -ComputerName $ip -Port $port -WarningAction SilentlyContinue
        if ($tcp.TcpTestSucceeded) {
            Write-Host "$ip has $port open"
        }
    } catch {}
}
```

### Slow scan (circa 2023)
```powershell
$subnet = Read-Host "Enter the subnet in dot-decimal notation (e.g. 192.168.1.): "
$start = Read-Host "Enter the starting value of the last octet in the range of IPs to include (e.g. 1): "
$end = Read-Host "Enter the ending value of the last octet in the range of IPs to include (e.g. 254): "

$ips = @()

for ($i = [int]$start; $i -le [int]$end; $i++) {
    $ip = $subnet + $i
    $ips += $ip
}

$results = foreach ($ip in $ips) {

    $hostname = $null
    try {
        $hostname = [System.Net.Dns]::GetHostEntry($ip).HostName
    } catch {}

    Write-Host "Testing host $ip ($hostname)"

    $ping = Test-Connection -ComputerName $ip -Count 1 -ErrorAction SilentlyContinue
    
    [PSCustomObject]@{
        IPAddress = $ip.ToString()
        Hostname = $hostname
        Status = if ($ping) {"Online"} else {"Offline"}
    }
}

# Print results
$results | Format-Table -AutoSize
```

### Find hosts running WinRM
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