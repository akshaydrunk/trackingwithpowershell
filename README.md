# tracking with powershell

**Process tracking**
 
Get Process by Name, PId, ParentPId, Path, Commandline
 ```
  Get-CimInstance -Class Win32_Process | Select-Object -Property Name, ProcessId, ParentProcessId, Path, CommandLine
 ```

Get information for a specific process
```
  Get-CimInstance -Class Win32_Process | Select-Object -Property Name, ProcessId, ParentProcessId, Path, CommandLine | Where-Object -Property Name -E "explorer.exe"
```

**Network tracking**

Display the list of related Windows services that are using the network
```
  Get-WmiObject Win32_Service | Where-Object -Property ProcessId -In (Get-NetTCPConnection).OwningProcess | Where-Object -Property State -eq Running | Format-Table ProcessId, Name, Caption, StartMode, State, Status, PathName
```

Resolved all host IP addresses to DNS names and specified process names for all connections
```
  Get-NetTCPConnection -State Established |Select-Object -Property LocalAddress, LocalPort,@{name='RemoteHostName';expression={(Resolve-DnsName $_.RemoteAddress).NameHost}},RemoteAddress, RemotePort, State,@{name='ProcessName';expression={(Get-Process -Id $_.OwningProcess). Path}},OffloadState,CreationTime |ft
```

**etc**
```
  Get-NetTCPConnection | Select-Object LocalAddress,LocalPort,RemoteAddress,RemotePort,State,OwningProcess | Format-Table
  Get-Process -Name msedge | ForEach-Object { Get-NetTCPConnection -OwningProcess $_.Id -ErrorAction SilentlyContinue }
  Get-Process -Id 11464
  Get-NetTcpConnection -OwningProcess 18948
  
```
