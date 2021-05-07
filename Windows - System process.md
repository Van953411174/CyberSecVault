# System
**PID** is always **4** for System and not assigned at random like other processes. 
 
 > "_The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread. System threads have all the attributes and contexts of regular user-mode threads (such as a hardware context, priority, and so on) but are different in that they run only in kernel-mode executing code loaded in system space, whether that is in Ntoskrnl.exe or in any other loaded device driver. In addition, system threads don't have a user process address space and hence must allocate any dynamic storage from operating system memory heaps, such as a paged or nonpaged pool._"

\- #Windows Internals 6th Edition

## Normal Baseline
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

## Unusual Behavior
-   A parent process (aside from System Idle Process (0))
-   Multiple instances of System. (Should only be 1 instance) 
-   A different PID. (Remember that the PID will **always** be PID 4)
-   Not running in Session 0