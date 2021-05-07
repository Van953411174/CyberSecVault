# Core Processes in Windows
Understanding core processes in #Windows is essential to understand and recognized normal behavior within a Windows OS, and consequently enable the ability to better recognize #malware and malicious processes, a core ability when #threat_hunting

See [[Windows - User mode and kernel mode]] for explanation on the two

#blue_team  **NOTE: All executables are in `C:\Windows\System32` except for Explorer.exe (which is `C:\Windows`)

## Core Processes
- [[Windows - System process|System process]]
- [[Windows - smss.exe|smss.exe]]
- [[Windows - csrss.exe|csrss.exe]]
- [[Windows - wininit.exe|wininit.exe]]
- [[Windows - services.exe|services.exe]]
- [[Windows - svchost.exe|svchost.exe]]
- [[Windows - lsass.exe|lsass.exe]]
- [[Windows - winlogon.exe|winlogon.exe]]
- [[Windows - explorer.exe|explorere.exe]]

Other processes with Windows 10 is RuntimeBroker.exe and taskhostw.exe (formerly taskhost.exe and taskhostex.exe).

## Sources/Readings
Material from: https://tryhackme.com/room/btwindowsinternals

Links to sources from which information was aggregated:
-   [https://www.threathunting.se/tag/windows-process/](https://www.threathunting.se/tag/windows-process/)[](https://www.threathunting.se/tag/windows-process/)
-   [https://www.sans.org/security-resources/posters/hunt-evil/165/download](https://www.sans.org/security-resources/posters/hunt-evil/165/download)[](https://www.sans.org/security-resources/posters/hunt-evil/165/download)
-   [https://docs.microsoft.com/en-us/sysinternals/resources/windows-internals](https://docs.microsoft.com/en-us/sysinternals/resources/windows-internals)