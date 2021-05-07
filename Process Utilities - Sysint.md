# Sysinternals - Process Utilities
#Windows [[Windows - Sysinternals Suite|Sysinternals Suite]] category covering Process Utilities. This note is **non-exhaustive**

Link to full category tools: https://docs.microsoft.com/en-us/sysinternals/downloads/process-utilities

## Autoruns
> "This utility, which has the most comprehensive knowledge of auto-starting locations of any startup monitor, shows you what programs are configured to run during system bootup or login, and when you start various built-in Windows applications like Internet Explorer, Explorer and media players. These programs and drivers include ones in your startup folder, Run, RunOnce, and other Registry keys. **Autoruns** reports Explorer shell extensions, toolbars, browser helper objects, Winlogon notifications, auto-start services, and much more. Autoruns goes way beyond other autostart utilities."

\- Autoruns [page](https://docs.microsoft.com/en-us/sysinternals/downloads/tcpview)

This is a good #blue_team  tool to search for any malicious entries created in the local machine to establish persistence.


## ProcDump
> "**ProcDump** is a command-line utility whose primary purpose is monitoring an application for CPU spikes and generating crash dumps during a spike that an administrator or developer can use to determine the cause of the spike."

\- ProcDump [page](https://docs.microsoft.com/en-us/sysinternals/downloads/procdump)

Alternatively you can use [[Process Utilities - Sysint#Process Explorer|Process Explorer]] to do the same.

The official page also has examples of usage and options.

## Process Explorer
>"The **Process Explorer** display consists of two sub-windows. The top window always shows a list of the currently active processes, including the names of their owning accounts, whereas the information displayed in the bottom window depends on the mode that Process Explorer is in: if it is in handle mode you'll see the handles that the process selected in the top window has opened; if Process Explorer is in DLL mode you'll see the DLLs and memory-mapped files that the process has loaded."

\- Process Explorer [page](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer)

Alternative is [[Process Hacker]]

Some useful column options:
- Run at Logon
- Replace Task Manager
- Verify Signatures

Uses a color code scheme to display additional information:
- Purple
	- files *may* be packed
- Red
	- is exiting / being stopped
- Green
	- freshly spawned (Just loaded)
- Light blue
	- processes run by the same account that start Process Explorer
- Dark Blue
	- process is selected (clicking or otherwise)
- Pink
	- Process is a service (ex: svchost.exe)
- Grey
	- Suspended process

## Process Monitor
> "Process Monitor is an advanced monitoring tool for Windows that shows real-time file system, Registry and process/thread activity. It combines the features of two legacy Sysinternals utilities, Filemon and Regmon, and adds an extensive list of enhancements including rich and non-destructive filtering, comprehensive event properties such as session IDs and user names, reliable process information, full thread stacks with integrated symbol support for each operation, simultaneous logging to a file, and much more. Its uniquely powerful features will make Process Monitor a core utility in your system troubleshooting and malware hunting toolkit."

\- Process Monitor [page](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon)

ProcMon will capture thousands of events occuring within the OS. The option to capture events can be toggled on and off. 

**Effective Use**
To use ProcMon effectively you **must** use the the Filter and **must** configure it properly

Guide to configure ProcMon: https://adamtheautomator.com/procmon/

## PsExec
> "**PsExec** is a light-weight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications, without having to manually install client software. PsExec's most powerful uses include launching interactive command-prompts on remote systems and remote-enabling tools like IpConfig that otherwise do not have the ability to show information about remote systems."

\- PsExec [page](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)

Guide to PsExec: https://adamtheautomator.com/psexec-ultimate-guide/

Another tool used by #threat_actors and has [[ATT&CK Framework]] entries
- MITRE ID is [S0029](https://attack.mitre.org/software/S0029/)
- Techniques:
	- [T1570](https://attack.mitre.org/techniques/T1570) (**Lateral Tool Transfer**)
	- [T1021.002](https://attack.mitre.org/techniques/T1021/002) (**Remote Services: SMB/Windows Admin Shares**)
	- [T1569.002](https://attack.mitre.org/techniques/T1569/002) (**System Services: Service Execution**)