[[Security Utilities - Sysint]]
# Sysmon - System Monitor
[Sysmon Resources](https://github.com/jymcheong/SysmonResources)

#Windows system service and device driver that remains resident across system reboots to montior  and log system activity to the [[Windows - Event Logs]]. This is very helpful for #continuous_monitoring. 

Can be used in #blue_team operations, with #SIEM agents to identify malicious activity attributed to #threat_actors and #malware.

When installed on an endpoint Sysmon will start early in the boot process. 

**EVENT LOG**
Events within Sysmon are stored in ***Applications and Services Logs/Microsoft/Windows/Sysmon/Operational***
## Installation
Sysmon is part of [[Windows - Sysinternals Suite]] and can be downloaded from the Sysinternals page or via the PowerShell command:
```powershell
Download-SysInternalsTools <path-to-download-to>
```

## Config
Sysmon requires a config file to tell the binary how to analyze events its receiving. There are plenty of pre-configured configs out there:
- [Sysmon-Config](https://github.com/SwiftOnSecurity/sysmon-config). 
- [ION-Storm config file](https://github.com/ion-storm/sysmon-config/blob/develop/sysmonconfig-export.xml). (proactive)

When creating/modifying config files a majority of ruels in symon-config will exclude events *rather* than include. This helps filter out normal activity in your environment. Configs are written in #XML

There are rulesets like *ION-Storm* sysmon-config fork that takes a more proactive approach with its ruleset by using a lot of include rules. 

**Understand your config!**
Its more important to thoroughly understand the config you're using before deploying in a production environment. *ION-Storm* for example excludes port 53 as an event, **HOWEVER** adversaries have started using port 53 as part of their malware/payloads, thereby the default config would let these events go undetected. 

**Changing Configs**
_NOTE : At any time you can change the configuration file used by uninstalling or updating the current configuration and replacing it with a new configuration file. For more information look through the Sysmon help menu._
## Starting Sysmon
Run the following #Powershell as Admin. This will accept the EULA and use the specified config file. 
```powershell
Sysmon.exe -accepteula -i sysmonconfig-export.xml
```



## Event IDs
Sysmon uses 24 different types of Event IDS all of which can be used within the config to specify how the events should be handled/analyzed. 

The following covers some key IDs

### ID 1: Process Creation
Any processes that have been created. Can use to look for known suspicious processes or processes *with typos* that would be considered an anomaly. 

Event uses CommandLine and Image XML Tags

*Code snippet specifying the Event ID to pull from as well as what condition to look for. In this case it is excluding svchost.exe process from the event logs*
```xml
<RuleGroup name="" groupRelation="or">
	<ProcessCreate onmath="exclude">
		<CommandLine condition="is">C:\Windows\system32\svchost.exe -k appmodel -p -s camsvc</CommandLine>
	</ProcessCreate>
</RuleGroup>
```
[[sysmon-event_id_1-process-creation.png]]
### ID 3: Network Connection
Events that occur remotely. Will include files and sources of suspicious binaries as well as opened ports. Event uses Image and DestinationPort XML tags. 

*Code snipped includes two ways to identify suspicious network connection activity. First will identify files transmitted over open ports (in this case specifically looking for nmap.exe which will then be reflected within the event logs). Second identifies open ports and specifically port 4444 which is commonly used with #metasploit. If the condition is met an event will be created and ideally trigger an alert.*
```xml
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
		<Image condition="image">nmap.exe</Image>
		<DestinationPort name="Alert,Metasploit" condition="is">4444</DestinationPort>
	</NetworkConnect>
</RuleGroup>
```
[[sysmon-event_id_3-network-connection.png]]

### ID 7: Image Loaded
Will look for #DLL's loaded by processes, which is useful for hunting for #DLL_Injection and #DLL_Hijacking attacks. ***CAUTION***: This event ID causes high system load. 
Event will use the Image, Signed, ImageLoaded, and Signature XML tags.

*Code snipped will look for any DLLs that have been loaded within the \\Temp\\ directory. If DLL is loaded within directory it can be considered anomaly and should be further investigated. *
```xml
<RuleGroup name="" groupRelation="or">
	<ImageLoad onmatch="include">
		<ImageLoaded condition="contains">\Temp\</ImageLoaded>
	</ImageLoad>
</RuleGroup>
```

[[sysmon-event_id_7-image-loaded.png]]

### ID 8: CreateRemoteThread
Will monitor for processes injecting code into other processes. The *CreateRemoteThread* function is used for **legitimate** tasks and apps. However, it *could* be used by #malware to hide malicious activity. This event will uses SourceIMage, TargetImage, StartAddress, and StartFunction XML tags. 

*Code snipped shows two ways of monitoring for CreateRemoteThread. First will look at memory address for a specific ending condition which could be an indicator of a #Cobalt_Strike beacon. Second will look for injected processes that do not have a parent process.*
```xml
<RuleGroup name="" groupRelation="or">
	<CreateRemoteThread onmatch="include">
		<StartAddress name="Alert,Cobalt Strike" condition="end with">0B80</StartAddress>
		<SourceImage condition="contains">\</SourceImage>
	</CreateRemoteThread>
</RuleGroup>
```
[[sysmon-event_id_8-createremotethread.png]]

### ID 11: File Created
Will log evnts when files are created or overwritten the endpoint. Could be used to identify file names and signatures of files that are written to disk. Event uses TargetFilename XML tags. 

*Code snippet is an example of a ransomware event monitor*
```xml
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
		<TargetFilename name="Alert,Ransomware" condition="contains">HELP_TO_SAVE_FILES</TargetFilename>
	</FileCreate>
</RuleGroup>
```
[[sysmon-event_id_11-file-created.png]]

### ID 12/13/14: Registry Event
Looks for changes or modifications to the registry. Malicious activity from the registry can include persistence and credential abuse. Event uses TargetObject XML tags. 

*Code Snippet will look for registry objects that are in "Windows\\System\\Scripts" directory as this is a common directory for adversaries to place scripts to establish persistence*
```xml
<RuleGroup name="" groupRelation="or">
	<RegistryEvent onmatch="include">
		<TargetObject name="T1484" condition="contains">Windows\System\Scripts</TargetObject>
	</RegistryEvent>
</RuleGroup>

```
[[sysmon-event_id_12_13_14-registry-event.png]]

### ID 15: FileCreateStreamHash
Will look for any files created in an alternate data stream ([[ADS- Alternate Data Streams|ADS]]), a common technique used by adversaries to hide malware. Event uses TargetFilename XML tags. 

*Code snipped will look for files with the .hta extension that have been placed w/i an #ADS 
```xml
<RuleGroup name="" groupRelation="or">
	<FileCreateStreamHash onmatch="include">
		<TargetFilename condition="end with">.hta</TargetFilename>
	</FileCreateStreamHash>
</RuleGroup>

```
[[sysmon-event_id_15-filecreatestreamhash.png]]

### ID 22: DNS Event
Will log all DNS queries and events for analysis. The most common way to deal w/ these events is to exclude all trusted domains that you know will be very common "noise" in environment. Event uses QueryName XML tags.

*Code snippet will exclude any DNS events with .microsoft.com query. This will remove that noise from the environment.*
```xml
<RuleGroup name="" groupRelation="or">
	<DnsQuery onmatch="exclude">
		<QueryName condition="end with">.microsoft.com</QueryName>
	</DnsQuery>
</RuleGroup>
```
[[sysmon-event_id_22-dns-event.png]]
## Best Practices
Very open and configurable platform but that there are generally a few best practices to implement.
- **Exclude > Include**
	- typically best to prioritize excluding events rather than including events. Prevents you from accidentally missing crucial events and only seeing the events that matter the most 
-  **CLI gives you further control**
	-  CLI give you the most control and filtering allowing for granular control. 
	-  You can either use [[Get-WinEvent]] or [[wevtutil.exe]] to access and filter logs. 
	-  As you incorporate Sysmon into your SIEM or other detection solutions these tools will become less used and needed. 
-  **Know your environment before implementation**
	-  Important when implementing any platform/too. Allows you to understand what is *normal* or *suspicious* to effectively craft your rules. 

## Threat Hunting
#threat_hunting #blue_team 
### Hunting Metasploit
#metasploit by default uses port 4444 or 5555 for meterpreter shells. 

You can use a [[Sysmon#ID 3 Network Connection|NetworkConnect, Event ID 3]] for detect these default ports. 
```xml
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
		<DestinationPort name="Alert,Metasploit-4444" condition="is">4444</DestinationPort>
		<DestinationPort name="Alert,Metasploit-5555" condition="is">5555</DestinationPort>
	</NetworkConnect>
</RuleGroup>
```
### Hunting Mimikatz
#Mimikatz commonly used to dump credentials from memory, mainly known for dumping LSASS (see [[Windows - lsass.exe]]). 

Technique and software on ATT&CK [T1055](https://attack.mitre.org/techniques/T1055/) and [S0002](https://attack.mitre.org/software/S0002/).

#### Detecting FIle Creation
Basic technique but still viable for deployments that have evaded AV. 
**Detect Files named mimikatz**
```xml
<RuleGroup name="" groupRelation="or">
	<FileCreate omatch="include">
		<TargetFileName condition="contains">mimikatz</TargetFileName>
	</FileCreate>
</RuleGroup>
```

#### Hunting Abnormal LSASS Behavior
We can use the **_ProcessAccess_** event ID to hunt for abnormal #LSASS behavior. This event along with LSASS would show potential LSASS abuse which usually connects back to Mimikatz some other kind of credential dumping tool.

If LSASS is access by a process other than *svchost.exe* it should be suspicious.
*Exclude svchost and include lsass*
```xml
<RuleGroup name="" groupRelation="or">  
	<ProcessAccess onmatch="exclude">  
		<SourceImage condition="image">svchost.exe</SourceImage>  
	</ProcessAccess>  
	<ProcessAccess onmatch="include">  
		<TargetImage condition="image">lsass.exe</TargetImage>  
	</ProcessAccess>  
</RuleGroup>
```

### Hunting RATs and C2 Servers
#RATs (or Remote Access Trojans) typically come with Anti-Virus and detection evasion techniques. They normally operate with a Client-Server model, #C2 (Command&Control) servers. 
 
**Common Back Ports Detection**
Using known common back ports we can create configuration files to detect. 

*Snipped from ION-Storm config that excludes common network connections like OneDrive.exe*
```xml
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
		<DestinationPort condition="is">1034</DestinationPort>
		<DestinationPort condition="is">1604</DestinationPort>
	</NetworkConnect>
	<NetworkConnect onmatch="exclude">
		<Image condition="image">OneDrive.exe</Image>
	</NetworkConnect>
</RuleGroup>
		
```

### Hunting Persistence
There are a multitude of ways to establish persistence but two common ways are* #registry modification* and *startup scripts*. These can be detected via [[Sysmon#ID 11 File Created|File Creation (ID 11)]] and Registry Modification (see [[Sysmon#ID 12 13 14 Registry Event]]). 

#### Startup
A Technique for startup script persistence - ATT&CK [T1547](https://attack.mitre.org/techniques/T1547/).
Using *SwiftOnSecurity* detects for files placed in *\\Startup\\* and *\\Start Menu* directories. 

*Establishes two rules, T1023 and T1165, that alert on files created in \\Start Menu and \\Startup respectively*
```xml
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
		<TargetFilename name="T1023" condition="contains">\Start Menu</TargetFilename>
		<TargetFilename name="T1165" condition="contains">\Startup]</TargetFilename>
	</FileCreate>
</RuleGroup>
```

#### Registry Key
Technique for persistence via #registry_keys - ATT&CK [T1112](https://attack.mitre.org/techniques/T1112/).
*SwiftOneSecurity* detection for registry modification that places a script inside *CurrentVersion\\Windows\\Run* and other registry locations. 

_Alerts on Registry Events involving **CurrentVersion\\Run**, **Group Policy\\Scripts**, **CurrentVersion\\Windows\\Runs**_
```xml
<RuleGroup name="" groupRelation="or">
	<RegistryEvent onmatch="include">
		<TargetObject name="T1060,RunKey" condition="contains">CurrentVersion\Run</TargetObject>
		<TargetObject name="T1484" condition="contains">Group Policy\Scripts</TargetObject>
		<TargetObject name="T1060" condition="contains">CurrentVersion\Windows\Runs</TargetObject>
	</RegistryEvent>
</RuleGroup>
```

### Hunting Evasion Techniques
Some examples of evasion techniques are #ADS- Alternate Data Streams, Injections, Masquerading, Packing/Compression, Recompiling, Obfuscation, Anti-Reversing.

#### Alternate Data Streams
Technique for evasion with ADS - ATT&CK [T1564](https://attack.mitre.org/techniques/T1564/004/)
Sysmon Event - [[Sysmon#ID 15 FileCreateStreamHash|FileCreateStreamHash (ID 15)]]

[[ADS- Alternate Data Streams]] are a common method of hiding macro/scripts/data. Event ID 15 will hash and log any NTFS Streams that are included within the Sysmon configuration file. 
*SwiftOnSecurity* config file snippet below detects for files in *Temp* and *Downlaods* as well as *.hta* and *.bat* extensions. 

```xml
<RuleGroup name="" groupRelation="or">
	<FileCreateStreamHash onmatch="include">
		<TargetFilename condition="contains">Downloads</TargetFilename>
		<TargetFilename condition="contains">Temp\7z</TargetFilename>
		<TargetFilename condition="ends with">.hta</TargetFilename>
		<TargetFilename condition="ends with">.bat</TargetFilename>
	</FileCreateStreamHash>
</RuleGroup>
```



#### Injection
Technique for Process Injection - ATT&CK [T1055](https://attack.mitre.org/techniques/T1055/).
Sysmon Event -  [[Sysmon#ID 8 CreateRemoteThread|CreateRemoteThread (ID 8)]] 
- *CreateRemoteThread* can be accessed using *OpenThread* and *ResumeThread*
	- This is used in DLL Injection, Thread Hijacking, and Process Hollowing. 

*Code snipped will exclude common remote threads w/o including specific attributes allowing for a more open and precise event rule*
```xml
<RuleGroup name="" groupRelation="or">
	<CreateRemoteThread onmatch="exclude">
		<SourceImage condition="is">C:\Windows\system32\svchost.exe</SourceImage>
		<TargetImage condition="is">C:\Program Files (x86)\Google\Chrome\Application\chrome.exe</TargetImage>
	</CreateRemoteThread>
</RuleGroup>
```


