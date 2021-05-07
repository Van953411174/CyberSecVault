# services.exe - Service Control Manager
Detailed information here: [Service_Control_Manager](https://en.wikipedia.org/wiki/Service_Control_Manager)

#Windows core process, primary responsibility is to handle system services: loading services, interacting with services, starting/ending services, etc.

Known as the #SCM or Service Control Manager

It maintains a database that can be queried using a Windows built-in utility [[sc.exe]]

**Registry Manipulation**
Information regarding *services.exe* is stored in the #registry under   `HKLM\System\CurrentControlSet\Services`

*services.exe* also loads device drivers marked as **auto-start** into memory.

When a user logs into a machine successfully, this process is responsible for setting the value of #registry_keys `Last Known Good` control set (*Last Known Good Configuration*), `HKLM\System\Select\LastKnownGood` to that of the `CurrentControlSet`

**Parent Process To...**
- [[Windows - svchost.exe]]
- spoolsv.exe
- msmpeng.exe
- dllhost.exe
- and more


## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\services.exe
- **Parent Process**:  wininit.exe
- **Number of Instances**:  One
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time

![Process Hacker - services.exe](https://assets.tryhackme.com/additional/windows-processes/services.png)
![Process Hacker - services.exe-cont](https://assets.tryhackme.com/additional/windows-processes/services2.png)

## Unusual Behavior
What is unusual?
-   A parent process other than wininit.exe
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process in plain sight
-   Multiple running instances
-   Not running as SYSTEM