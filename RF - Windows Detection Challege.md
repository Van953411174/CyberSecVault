<- [[RangeForce - SOC Detection Challenges Index]]
# Windows Detection Challenge - RangeForce

_**Scenario**_: 
> You are working in Commensurate Technology as a SOC analyst.
Commensurate Tech is using Windows Security (formerly Windows Defender) as the antivirus solution and the computers are configured to gather various events using Sysmon and Splunk forwarder in combination to send logs to a central Splunk server.
Monthly Security Health Check & Compliance Report indicates that Windows Security has been **disabled** on the host **windows10**.
You are tasked with making sure whether this issue is just a technical glitch or not. You are also provided with a list of possible indicators of compromise (IOCs) to aid in your intelligence gathering.
You need to **find out** whether it was **disabled for malicious purposes** and if so, **what was done during the time the computer was compromised**.
You will investigate the possible compromise using Splunk SIEM solution.

See [[Splunk]]

**Tasks**
1.[[RF - Windows Detection Challege#Restore Windows Security|Restore Windows Security]]
2. [[RF - Windows Detection Challege#Search for IOCs|Search for IOCs]]
3. [[RF - Windows Detection Challege#Gather Intelligence|Gather Intelligence]]
4. [[RF - Windows Detection Challege#Analyze Malware Stager|Analyze Malware Stager]]
5. [[RF - Windows Detection Challege#Delete the Stager|Delete the Stager]]


## Restore Windows Security
Since in the scenario, this has occurred before, there are already pre-built scripts to re-enable Windows Security in registry(`C:\Tools\01. Enable-WinSecurity-registry.ps1`) and restore the Windows Security functionality (`C:\Tools\02. Restore-WinSecurity-functionality.ps1`).
Order of operation:
1. `01. Enable-WinSecurity-registry.ps1`
2. `Restart Now` in Windows Security GUI
3. `02. Restore-WinSecurity-functionality.ps1`

## Search for IOCs
> You received an updated list of IOCs for today. Your daily process is to use Splunk to look for these IOCs and if any match in your environment.
192.168.0.126
192.168.0.157
192.168.0.212
192.168.0.214

After logging into Splunk, use query:
`192.168.0.126 OR 192.168.0.157 OR 192.168.0.212 OR 192.168.0.214`

Which returns hits for `192.168.0.212`
![[RF-Windows-Detection-Challenge-Splunk-IOC-results.png]]


## Gather Intelligence
Pivot/sift through Splunk to investigate the event. 

**What is the _filename_ of the malware stager?**
- `LaunchPad.bat`
- Process
	- Start at oldest chronological hit
	- Check for interesting fields like "ParentCommandLine", which calls a program called `LaunchPad.bat` ![[RF-Windows-Detection-Challenge-splunk-stager.png]]

**What type of _startup method_ was used to execute the stager?**

| Startup Method    | Yes/No |
| ----------------- | ------ |
| Registry          | X      |
| User Startup      | X      |
| All Users Startup | ==Y==      |
| WMI               | X      |
| Scheduled Tasks   | X      |


- Process
	- From oldest event discovered in *What is the filename...*, expand and examine. 
	- Looking at the *ParentCommandLine* again, as well as *Message*, we see that the path of the stager is `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\`, indicating the stager is starting for **ALL**  users, since user specific startup entries are located in *AppData* (`%AppData%\Microsoft\Windows\Start Menu\Programs\Startup`) ![[RF-Windows-Detection-Challenge-splunk-stager-all-user-startup.png]]

**What is the _full path_ of the archive that was created for data exfiltration?**
`C:\Windows\Temp\g0nna3XF1l7h15.tAR`

- Process
	- Seeing archived implied to me that there was some compression taking place, as would be likely during exfiltration. 
	- Adding `archive` to the Splunk query (now `192.168.0.212 archive`) returned events indicating usage of *bsdtar* archive tool
	- From there, examining the event revealed the path where the archive was created ![[RF-Windows-Detection-Challenge-splunk-exfiltration-archive.png]]

**What _application_ was used for the data exfiltration attempt?**
`scp.exe`

- Process
	- Adjust Splunk query to include the name of the archive which would likely be used in an attempt to exfiltrate it (now `192.168.0.212 g0nna3XF1l7h15`)
	- The first event returned shows that `scp.exe` attempted to copy the archive to the remote destination `user001@192.168.0.190:/home/user001/pkg.tar` ![[RF-Windows-Detection-Challenge-splunk-data-exfil-application.png]]

**What is the _password_ for the new malicious user?**
`password123@`

- Process
	- Adjust Splunk query to include `user` and `add` to find the creating of the user (where the password should be specified) ![[RF-Windows-Detection-Challenge-splunk-new-user-password.png]]

**How was the _malicious user_ added to _a group_? (Full command)**
`"C:\Windows\system32\net.exe" localgroup Administrators Palware /add`

- Process
	- Adjust Splunk query to include `Palware` and `add` to find where Palware has been added, e.g. to a group! ![[RF-Windows-Detection-Challenge-splunk-Palware-added-to-group.png]]

## Analyze Malware Stager
>After extracting all the necessary information from the IOCs you want to do dynamic analysis of the malware.
The windows10 host is currently in a controlled environment. Windows Security has been restarted and its features are re-enabled. Knowing the malware stager location, you need to execute the stager and see what Windows Security will show.
**Note: VirTool:Win32/DefenderTamperingRestore** detection is related to Windows Security being disabled and re-enabled for the playthrough of this scenario, so in this particular case, ignore that detection.
*DISCLAIMER: You generally should not execute any malware in an environment where it can spread within the network. Make sure that necessary precautions are in effect. For the purposes of the scenario consider the RangeForce module a sandbox.*

*Execute stage executable, use PowerShell or Windows Security GUI to answer the questions*

**What is the severity of the detection?**
`Severe`
**What is the name of the threat?**
`VirTool:PowerShell/Empire.A`

- Process
	- Run the `.bat` stager and check the Windows Security GUI which will list the detection and details ![[RF-Windows-Detection-Challenge-stager-threat-detected-windows-security.png]]

## Delete the Stager


