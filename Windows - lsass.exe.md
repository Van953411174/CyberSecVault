# lsass.exe - Local Security Authority Subsystem Service

#LSASS is a process in #Windows that is responsible for enforcing the security policy on the system. It verifies uses logging on to a Windows computer or server, handles password changes, and creates access tokens. It also writes to the Windows Security Log. Used in [[Windows - Authentication]] for Local Auth. 

It creates security tokes for #SAM ([[Security Account Manager]]), #AD ([[Active Directory]]), and #NETLOGON 

It uses authentication packages specified in the #registry at `HKLM\System\CurrentControlSet\Control\Lsa`

**Frequent Target**
Common tools like #Mimikatz are used to dump credentials or they mimic this process to hide in plain sight. This is done by either naming #malware by this process name or simply misspelling the malware slightly.

**How LSASS *is maliciously used + mitigations by #Microsoft** : https://yungchou.wordpress.com/2016/03/14/an-introduction-of-windows-10-credential-guard/

## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\lsass.exe
- **Parent Process**:  [[Windows - wininit.exe|wininit.exe]]
- **Number of Instances**:  One
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time

![Process Hacker - lsass.exe](https://assets.tryhackme.com/additional/windows-processes/lsass.png)


## Unusual Behavior
What is unusual?
-   A parent process other than wininit.exe
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process in plain sight
-   Multiple running instances
-   Not running as SYSTEM