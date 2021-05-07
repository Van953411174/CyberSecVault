# svchost.exe - Service Host (Host Process for Windows Service)
Detailed Information: [Svchost.exe](https://en.wikipedia.org/wiki/Svchost.exe)
#Windows process responsible for hosting and managing Windows services.

The services running in this process are implemented as #DLL's. The DLL to implement is stored in the #registry for the service under the #registry_keys `Parameter` in `ServiceDLL`.
Full path is: `HKLM\SYSTEM\CurrentControlSet\Services\SERVICE NAME\Parameters`

Each service called will load its own DLL, so its not unusual when investigating for there to be different DLL paths for each service. 

**Frequent Target**
Since *svchost.exe* will always have multiple running processes on any Windows system, this process has been a target or malicious use. Adversaries create #malware to masquerade as this process and try to hide amongst the legitimate svchost.exe processes. They can name the malware svchost.exe or misspell it slightly, such as scvhost.exe. By doing so the intention is to go under the radar. Another tactic is to install/call a malicious service ( #DLL).

**Homomorphic Abuse of svchost.exe**
2015 Article on abuse of process and file names (https://www.hexacorn.com/blog/2015/12/18/the-typographical-and-homomorphic-abuse-of-svchost-exe-and-other-popular-file-names/)

## Normal Baseline

![Process Hacker - svchost.exe](https://assets.tryhackme.com/additional/windows-processes/svchost.png)
via [[Process Hacker]]
- **Image Path**: %SystemRoot%\\System32\\svchost.exe
- **Parent Process**: services.exe
- **Number of Instances**: Many
- **User Account**: Varies (SYSTEM, Network Service, Local Service) depending on the svchost.exe instance. In Windows 10 some instances can run as the logged-in user.
- **Start Time**: Typically within seconds of boot time. Other instances can be started after boot

**Windows 10 ver 1703 and Higher**
For WIndows 10 ver 1703 and up, on systems with more than 3.5 GB of memory, each service **will** run its own process and therefore **not** have a `-k` in the binary path. (Read more [here](https://en.wikipedia.org/wiki/Svchost.exe)) 

**Windows 10 lower than ver 1703**
The binary path **WILL** have a `-k` in the path. This is a good indicator of a legitimate *svchost.exe*
- `-k` is for grouping similar services to share the same process
	- concept was based on OS design to reduce resource consumption

## Unusual Behavior
What is unusual?
-   A parent process other than services.exe
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process in plain sight
-   The absence of the -k parameter