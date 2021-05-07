# explorer.exe - Windows Explorer
#Windows process that gives the user access to their folders and files, also provides functionality to other features such as the Start Menu, Taskbar, etc.

The [[Windows - winlogon.exe|winlogon process]] runs *userinit.exe* ([docs](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc939862(v=technet.10)?redirectedfrom=MSDN) which launches the value in #registry_keys `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell`. *Userinit.exe* exits after spawning *explorer.exe*. Because of this, *explorer.exe's* parent process is non-existent. 

*Explorer.exe* can spawn MANY child processes
*Example of child processes from explorere.exe*
![childprocesses](https://assets.tryhackme.com/additional/windows-processes/explorer-tree.png)

## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\explorer.exe
- **Parent Process**:  Created by userinit.exe and exits
- **Number of Instances**:  One or more per interactively logged-in user
- **User Account**:  Logged-in user(s)
- **Start Tim**e:  First instance when the first interactive user logon session begins

![Process Hacker - explorer.exe](https://assets.tryhackme.com/additional/windows-processes/explorer.png)

## Unusual Behavior
What is unusual?
-   An actual parent process. (userinit.exe calls this process and exits)
-   Image file path other than C:\\Windows
-   Running as an unknown user
-   Subtle misspellings to hide rogue process in plain sight
-   Outbound TCP/IP connections