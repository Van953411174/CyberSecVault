# Windows Utility Tools
## Built-in
Brief, non-exhaustive list, of some built-in utility tools
-   [[WIndows Utility Tools#Built-in#Computer Management|Computer Management]]
-   [[WIndows Utility Tools#Built-in#Local Security Policy|Local Security Policy]]
-   [[WIndows Utility Tools#Built-in#Disk Cleanup|Disk Cleanup]]
-   [[WIndows Utility Tools#Built-in#Registry Editor|Registry Editor]] ^c0c1a8
-   [[WIndows Utility Tools#Built-in#Command-line Tools|Command-line tools]]
-   [[WIndows Utility Tools#Built-in#Registry Editor Regedit|Registry Editor ( #Regedit)]]


### Computer Management
Contains tools such as:
 - [[WIndows Utility Tools#Built-in#Computer Management#Task Scheduler|Task Scheduler]]
-    [[WIndows Utility Tools#Built-in#Computer Management#Event Viewer|Event Viewer]]
-    [[WIndows Utility Tools#Built-in#Computer Management#Shared Folders|Shared Folders]]
-    [[WIndows Utility Tools#Built-in#Computer Management#Local users & computers|Local users & computers]]
-   [[WIndows Utility Tools#Built-in#Computer Management#Performance Monitor|Performance Monitor]]
-    [[WIndows Utility Tools#Built-in#Computer Management#Disk Management|Disk Management]]
-    [[WIndows Utility Tools#Built-in#Computer Management#Services & Applications|Services & Applications]]

#### Task Scheduler
This is a tool that allows predefined actions to be automatically executed whenever a certain set of conditions is met(Ex: You can set up a date and time for a piece of software to be installed, or a script to run).

#### Event Viewer
Probably one of the most important tools that come with Windows. The #Event_Viewer logs events that happen across the device (Ex: Successful & Failed login attempts, System Errors, etc). The reason Event Viewer is important is because it can be used to forward the events to a #SIEM (Security Information and Event Manager) which helps the IT team of a company determine possible malicious activities.
#### Shared Folders
Is a directory or a folder that can be shared across the network and can be accessed by multiple users.
#### Local users & computers
Using local users and computers we can create users, add them to different built-in groups, and they can be given different levels of access (Ex: User A can connect through RDP to a machine but user B can't).
#### Performance Monitor
Performance Monitor monitors the different activities across the device such as CPU usage, memory usage, etc.
#### Disk Management
Using Disk Management you can shrink, expand, create new partitions (drives) and format the partitions.
#### Services & Applications
It is possible to check the running services on the system and you have the ability to start, stop or restart them.

### Local Security Policy
Local Security Policy is a group of settings you can configure to strengthen the computer's security. Even though  
most policy settings in Windows are fine, there are a few that need adjusting for enhanced security. You can set the minimum password length, the password complexity level, you can disable guest & local administrator accounts, and many more.

==Note: If the computer is not integrated into an #AD environment disabling local administrator account is a bad idea.==

### Disk Cleanup
Another useful utility is Disk Cleanup. Using Disk Cleanup we can delete files that are no longer needed by the system and are just adding up to the computer disk space. Running Disk Cleanup as administrator we can also clean system files (Ex: sometimes, after getting updates some files remain on disk, but these are no longer needed).

To access Disk Cleanup right-click on Local Disk C and click Properties. You should see a button in the General tab named "Disk Cleanup".

![Disk Cleanup](https://i.imgur.com/8GdfO9P.png)

You just need to tick the box/files you want to clean and press OK.
### Registry Editor
The Windows #registry database stores many important operating system settings. For example, it contains entries with information about what should happen when double-clicking a particular file type or how wide the taskbar should be. Built-in and inserted hardware also stores information in the registry when the driver is installed; this driver is called up every time the system is booted up.

### Command-line Tools
Windows comes equipped with two command-line tools:

-   CMD
	-   #CMD is the command-line interpreter for Microsoft Windows operating systems used to automate various system-related tasks using scripts and batch files. Users can interact with the OS directly using text-based commands. It emulates most of the command line abilities available in #MS-DOS through a command-line interface.
-   Powershell
	-   #Powershell is mainly used by sysadmins to manage the network and domain they handle, as well as the computers and other devices that are part of it. #PowerShell is a scripting language. The PowerShell can interpret batch commands and Powershell commands, but the command prompt can only interpret batch commands.
-   Windows Terminal  
	-   Windows Terminal can be used instead of Powershell and CMD and can be installed from the Microsoft Store. The application includes multiple tab support, alongside themes and customization for developers who want to tweak the Terminal.
	-   Also integrates with #WSL and #WSL2 for seamless access to those systems
    
Both CMD and Powershell are powerful command-line tools used to automate system administration tasks by writing a script/batch file. However, CMD has limited administration capabilities as compared to Powershell, which, on the other hand, is a more advanced and modern shell implementation with additional features and enhancements (Ex: #cmdlets).

### Registry Editor (Regedit)
Registry Editor can be considered a database that contains low-level settings for Microsoft Windows settings and applications. The registries are structured as follows:
-   HKEY\_CLASSES\_ROOT
-   HKEY\_CURRENT\_USER
-   HKEY\_LOCAL\_MACHINE
-   HKEY\_USERS
-   HKEY\_CURRENT\_CONFIG

Values stored in registries are referred to as #registry_keys

#Powershell can directly browse registry with `cd <REG DB>`:
- Ex: `cd HKLM:\`

Windows also has a built in tool named `reg` which can be used from the command line to add, remove, query, import, export, etc #registry_keys 

The GUI version is called #Regedit 