# wininit.exe - Windows Initialization Process
Responsible for launching *[[Windows - Core Processes#services exe|services.exe]]* (Service Control Manager), *[[Windows - Core Processes#lsass exe|lsass.exe]]*  (Local Service Authority) and *lsaiso.exe* (associated with [[Credential Guard]] and Key Guard, will only be present if Credential Guard is enabled)

*wininit.exe* is another critical #Windows process that runs in the background along with its child processes. 

## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\wininit.exe
- **Parent Process**:  Created by an instance of smss.exe
- **Number of Instances**:  One
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time

**Session 0 (PID 496)**
![Process Hacker - wininit.exe](https://assets.tryhackme.com/additional/windows-processes/wininit.png)

## Unusual Behavior
What is unusual?
-   An actual parent process. (smss.exe calls this process and self-terminates)
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process in plain sight
-   Multiple running instances
-   Not running as SYSTEM

