# winlogon.exe - Windows Logon
Detailed information here: [Winlogon](https://en.wikipedia.org/wiki/Winlogon)

Process spawned by [[Windows - smss.exe|smss.exe]] along with a copy of [[Windows - csrss.exe|csrss.exe]]

#Windows process for handling the Secure Attention Sequence ( #SAS). This is the `CTRL+ALT+DELETE` combo users press to enter their username & password.

This process is also responsible for loading the user profile. This is done by loading the user's NTUSER.DAT into HKCU `registry` and via *userinit.exe* loads the user's shell. 
[Userinit](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc939862(v=technet.10)?redirectedfrom=MSDN)

## Normal Baseline
via [[Process Hacker]]
- **Image Path**:  %SystemRoot%\\System32\\winlogon.exe
- **Parent Process**:  Created by an instance of smss.exe that exits, so analysis tools usually do not provide the parent process name.
- **Number of Instances**:  One or more
- **User Account**:  Local System
- **Start Time**:  Within seconds of boot time for the first instance (for Session 1).  Additional instances occur as new sessions are created, typically through Remote Desktop or Fast User Switching logons.

![Process Hacker - winlogon.exe](https://assets.tryhackme.com/additional/windows-processes/winlogon1.png)
![Process Hacker - winlogon.exe - token](https://assets.tryhackme.com/additional/windows-processes/winlogon2.png)

## Unusual Behavior
What is unusual?
-   An actual parent process. (smss.exe calls this process and self-terminates)
-   Image file path other than C:\\Windows\\System32
-   Subtle misspellings to hide rogue process in plain sight
-   Not running as SYSTEM
-   Shell value in the registry other than explorer.exe