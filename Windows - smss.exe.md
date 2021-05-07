# smss.exe - Session Manager Subsystem
Process also known as #Windows Session Manager, is responsible for creating new sessions, and is a *user-mode* process started by the kernel. 

It starts the kernel mode and user mode of the [Windows subsystem](https://en.wikipedia.org/wiki/Architecture_of_Windows_NT), which includes:
- *win32k.sys* (kernel mode)
- *winsrv.dll* (user mode)
- *csrss.exe* (user mode) 

### Flow
Detailed process flow: [Session_Manager_Subsystem](https://en.wikipedia.org/wiki/Session_Manager_Subsystem)
**Smss.exe** starts *[[Windows - Core Processes#csrss exe Client Server Runtime Process|csrss.exe]]* (Windows subsystem) and *[[Windows - Core Processes#wininit exe|wininit.exe]]* in Session 0, an isolated Windows session for the OS, and *csrss.exe* and *[[Windows - Core Processes#winlogon exe|winlogon.exe]]* for Session 1, which is the user session. 

The first child instance creates child instances in new sessions. This is done by **smss.exe** copying itself into the new session and self-terminating. 

Additionally, any other subsystem listed in the `Required` value of `HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems` is also launched. #registry_keys 

Finally, **smss.exe** is also responsible for creating environment variables, virtual memory paging files and starts *winlogon.exe* (Windows Logon Manager)

**Session 0 (*csrss.exe & wininit.exe*)**
![Session 0](https://assets.tryhackme.com/additional/windows-processes/smss-session0b.png)
**Session 1 (*csrss.exe & winlogon.exe*)**
![Session 1](https://assets.tryhackme.com/additional/windows-processes/smss-session1b.png)
**Registry Key _Required_**
![Reg Key Required](https://assets.tryhackme.com/additional/windows-processes/smss-registry.png)


## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\smss.exe
- **Parent Process**:  System
- **Number of Instances**:  One master instance and child instance per session. The child instance exits after creating the session.
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time for the master instance
![Process Hacker - smss.exe](https://assets.tryhackme.com/additional/windows-processes/smss.png)

## Unusual Behavior
What is unusual?
-   A different parent process other than System(4)
-   Image path is different from C:\\Windows\\System32
-   More than 1 running process. (children self-terminate and exit after each new session)
-   User is not SYSTEM
-   Unexpected registry entries for Subsystem