# Windows

Linked Topics:
- [[Windows - Authentication]]
- [[WIndows - Utility Tools]]
- [[Windows - Core Processes]]




## General File System
#Windows File System Structure is:
- Logical Drives (Ex: Local Disk C)
- Folders (These are folders that come by default. EX: Documents, Downloads, Music)
- Files

Pertinent Folders on C Drive:
- PerfLogs
	- Stores the system issues and other reports regarding performance
- Program Files
	- location where programs install unless you change their path (Ex: Choosing to install software on D drive)
- Program Files (x86)
	- location where programs install unless you change their path (Ex: Choosing to install software on D drive)
- Users
	- where the users created are stored
	- also stores users generated data
		- Ex: saving a file on your Desktop
- Windows
	- Basically contains the code to run the operating system and some utility tools

## File Permissions
File perms can be set by an administrator or priv acct. These perms apply to 
- Users
- Groups

Permissions that can be set are:
- Full control
	- allows user/users/group/groups to set the ownership of the folder, set permissions for others, modify, read, write, and execute
- Modify
	- allows user/users/group/groups to modify, read, write, and execute
- Read & Execute
	- allows user/users/group/groups to read and execute
- List folders contents
	- allows user/users/group/groups to list the contents (files, subfolders, etc) of a folder
- Read
	- only allows user/users/group/groups to read files
- Write
	- allows user/users/group/groups to write data to the specified folder (automatically set when "Modify" permission is granted)
- Special Permissions

*NOTE: You can allow or deny permissions for users or groups*

### Tool
Windows has a tool `icacls` that can be used to check the files or folder permissions. It returns letters indicating permissions:
- `I` permission inherited from the parent container
- `F` full access (full control)
- `M` modify right/access
- `OI` object inherit
- `IO` inherit only
- `CI` container inherit
- `RX` read and execute
- `AD` append data (add subdirectories)
- `WD` write data and add files

It can also be use to set ownership and set, remove, or deny permission
with appended commands like `/grant` , `/deny`, `/setowner`

Full documentation:
https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/icacls




