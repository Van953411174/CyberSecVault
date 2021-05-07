# csrss.exe - Client Server Runtime Process
Detailed information here: [Server_Runtime_Subsystem](https://en.wikipedia.org/wiki/Client/Server_Runtime_Subsystem)
The user-mode side of #Windows Subsystem. This process is always running and is critical to system operation. If terminated it results in system failure. Also responsible for Win32 console window and process threat creation and deletion. 

For each instance *csrsrv.dll, basesrv.dll, and winsrv.dll* are loaded (along with others). 

This process is also responsible for making the Windows API available to other processes, mapping drive letters, and handling Windows shutdown process. 

Spawned by [[Windows - Core Processes#smss exe Session Manager Subsystem|smss.exe]] which self-terminates itself

## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\csrss.exe
- **Parent Process**:  Created by an instance of smss.exe
- **Number of Instances**:  Two or more
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time for the first 2 instances (for Session 0 and 1).  Start times for additional instances occur as new sessions are created, although often only Sessions 0 and 1 are created.

**Session 0 (PID 392)**
![Process Hacker - csrss.exe](https://assets.tryhackme.com/additional/windows-processes/csrss-session0.png)

**Session 1 (PID 512)**
![Process Hacker - csrss.exe](https://assets.tryhackme.com/additional/windows-processes/csrss-session1.png)

## Unusual Behavior
 What is unusual?
-   An actual parent process. (smss.exe calls this process and self-terminates)
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process masquerading as csrss.exe in plain sight
-   User is not SYSTEM