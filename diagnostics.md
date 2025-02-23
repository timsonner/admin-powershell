Get Top CPU-Consuming Processes  
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 -Property Id, ProcessName, CPU
```

Get Top RAM-Consuming Processes
```powershell
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10 -Property Id, ProcessName, WorkingSet
```

Get system uptime  
```powershell
(Get-Date) - (gcim Win32_OperatingSystem).LastBootUpTime
```

System Performance
```powershell
Get-WmiObject Win32_PerfFormattedData_PerfOS_System | Select-Object SystemUpTime, Processes, Threads, ProcessorQueueLength | Format-Table -Property *,@{Name="Uptime (Hours)";Expression={$_.SystemUpTime / 3600 -as [int]}} -AutoSize
```

Disk Space Usage (with GB)
```powershell
Get-PSDrive -PSProvider FileSystem | Where-Object Used -ne $null | Select-Object Name, @{Name="Used (GB)";Expression={[math]::Round($_.Used / 1GB, 2)}}, @{Name="Free (GB)";Expression={[math]::Round($_.Free / 1GB, 2)}} | Format-Table -AutoSize
```

CPU Load Percentage
```powershell
Get-WmiObject Win32_Processor | Select-Object DeviceID, @{Name="CPU Load (%)";Expression={$_.LoadPercentage}} | Format-Table -AutoSize
```

Performance Counters (with Enhanced Formatting)
```powershell
Get-Counter -Counter "\Processor(_Total)\% Processor Time", "\Memory\Available MBytes", "\System\Context Switches/sec" | 
    Select-Object -ExpandProperty CounterSamples | 
    Format-Table -Property Path, @{Name="Value";Expression={
        if ($_.Path -like "*\Memory\Available MBytes") {
            "{0:N2} MB" -f $_.CookedValue
        } elseif ($_.Path -like "*\% Processor Time") {
            "{0:N2} %" -f $_.CookedValue
        } else {
            $_.CookedValue
        }
    }} -AutoSize
```

Working set memory usage  
```powershell
Get-Process | 
    Sort-Object WS -Descending | 
    Select-Object -First 10 Name, 
        @{Name="Working Set (MB)";Expression={[math]::Round($_.WS / 1MB, 2)}}, 
        @{Name="CPU Time (Sec)";Expression={[math]::Round($_.CPU, 2)}} | 
    Format-Table -AutoSize
```  