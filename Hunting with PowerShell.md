[[Threat Hunting]]
## Hunting for Metasploit 
#metasploit  by default uses port `4444` and `5555` for meterpreter sessions. You can find these using #Powershell. 
Following example is for port `4444`. 

To hunt for open ports with PowerShell we will be using the PowerShell module [[Get-WinEvent]] along with [[XPath Query|XPath queries]]. We can use the same #XPath queries that we used in the rule to filter out events from `NetworkConnect` with `DestinationPort`. See [[Windows - Event Logs]]
```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'
```

We can break this command down by its filters to see exactly what it is doing. It is first filtering by `Event ID 3` which is the network connection ID. It is then filtering by the data name in this case `DestinationPort` as well as the specific port that we want to filter. We can adjust this syntax along with our events to get exactly what data we want in return.

## Hunting Mimikatz
To detect abnormal #LSASS behavior (see [[Windows - lsass.exe]]), indicative of #Mimikatz, you can use [[Get-WinEvent]] and [[XPath Query]]. 
```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'
```

## Hunting Common Back Connect Ports
Event ID from [[Sysmon#ID 3 Network Connection]]
Used in relation to #RATs and #C2 servers. Using  [[Get-WinEvent]] and [[XPath Query]]. 
```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=<Port>'`
```


## Hunting Evasion Techniques

### Detecting Alternate Data Streams
Using [[Sysmon#Alternate Data Streams]|Sysmon Hunting ADS] config file and Event ID 15, you can easily detect using PowerShell. 

```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=15'
```

### Detecting Injection via Remote Thread Creation
Using [[Sysmon#Injection|Sysmon Hunting Injection]] config using Event ID 8. 

```powershell
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=8'
```