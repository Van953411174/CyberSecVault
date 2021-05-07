# Core Processes in Windows
Understanding core processes in #Windows is essential to understand and recognized normal behavior within a Windows OS, and consequently enable the ability to better recognize #malware and malicious processes, a core ability when #threat_hunting . 

See [[Windows - User mode and kernel mode]] for explanation on the two
## System
**PID** is always **4** for System and not assigned at random like other processes. 
 
 > "_The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread. System threads have all the attributes and contexts of regular user-mode threads (such as a hardware context, priority, and so on) but are different in that they run only in kernel-mode executing code loaded in system space, whether that is in Ntoskrnl.exe or in any other loaded device driver. In addition, system threads don't have a user process address space and hence must allocate any dynamic storage from operating system memory heaps, such as a paged or nonpaged pool._"

\- Windows Internals 6th Edition

### Normal Baseline
via [[Process Explorer]] 
- **Image Path**:  N/A
- **Parent Process**:  None
- **Number of Instances**:  One
- **User Account**:  Local System
- **Start Time**:  At boot time
![Process Explorer - System](https://assets.tryhackme.com/additional/windows-processes/system.png)

via [[Process Hacker]]
- **Image Path**: C:\\Windows\\system32\\ntoskrnl.exe (NT OS Kernel)
- **Parent Process**: System Idle Process (0)
![Process Hacker - System](https://assets.tryhackme.com/additional/windows-processes/system2.png)

### Unusual Behavior
-   A parent process (aside from System Idle Process (0))
-   Multiple instances of System. (Should only be 1 instance) 
-   A different PID. (Remember that the PID will **always** be PID 4)
-   Not running in Session 0

## smss.exe (Session Manager Subsystem)
Process also known as Windows Session Manager, is responsible for creating new sessions, and is a *user-mode* process started by the kernel. 

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


### Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\smss.exe
- **Parent Process**:  System
- **Number of Instances**:  One master instance and child instance per session. The child instance exits after creating the session.
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time for the master instance
![Process Hacker - smss.exe](https://assets.tryhackme.com/additional/windows-processes/smss.png)

### Unusual Behavior
What is unusual?
-   A different parent process other than System(4)
-   Image path is different from C:\\Windows\\System32
-   More than 1 running process. (children self-terminate and exit after each new session)
-   User is not SYSTEM
-   Unexpected registry entries for Subsystem

## csrss.exe (Client Server Runtime Process)
Detailed information here: [Server_Runtime_Subsystem](https://en.wikipedia.org/wiki/Client/Server_Runtime_Subsystem)
The user-mode side of Windows Subsystem. This process is always running and is critical to system operation. If terminated it results in system failure. Also responsible for Win32 console window and process threat creation and deletion. 

For each instance *csrsrv.dll, basesrv.dll, and winsrv.dll* are loaded (along with others). 

This process is also responsible for making the Windows  API available to other processes, mapping drive letters, and handling Windows shutdown process. 

Spawned by [[Windows - Core Processes#smss exe Session Manager Subsystem|smss.exe]] which self-terminates itself
### Normal Baseline

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

### Unusual Behavior
 What is unusual?
-   An actual parent process. (smss.exe calls this process and self-terminates)
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process masquerading as csrss.exe in plain sight
-   User is not SYSTEM


## wininit.exe (Windows Initialization Process)
Responsible for launching *[[Windows - Core Processes#services exe|services.exe]]* (Service Control Manager), *[[Windows - Core Processes#lsass exe|lsass.exe]]*  (Local Service Authority) and *lsaiso.exe* (associated with Credential Guard and Key Guard, will only be present if Credential Guard is enabled)

*wininit.exe* is another critical Windows process that runs in the background along with its child processes. 

### Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\wininit.exe
- **Parent Process**:  Created by an instance of smss.exe
- **Number of Instances**:  One
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time

**Session 0 (PID 496)**
![Process Hacker - wininit.exe](https://assets.tryhackme.com/additional/windows-processes/wininit.png)

### Unusual Behavior
What is unusual?
-   An actual parent process. (smss.exe calls this process and self-terminates)
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process in plain sight
-   Multiple running instances
-   Not running as SYSTEM

## services.exe (Service Control Manager ( #SCM))
Primary responsibility is to handle system services: loading services, interacting with services, starting/ending services, etc.

It maintains a database that can be queried using a Windows built-in utility [[sc.exe]]
## svchost.exe
## lsass.exe
## winlogon.exe
## explorere.exe
