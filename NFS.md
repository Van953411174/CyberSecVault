# NFS
## What is it?
#NFS stands for Network File System
- Allows a system to share directories and files with others over a network
	- mounts all/portions of a file system on a server
	- portion mounted can be accessed by clients with whatever privileges are assigned to each file

## How does it work?
### Technical Overview: 


### General Overview
1. Client request to mount directory from a remote host on a local directory just the same way it can mount a physical device
2. Mount server will then act to connect to the relevant mount daemon using #RPC 
3. Server checks if user has perms to mount requested directory.
	1. Then returns file handle which uniquely identifies each file and directory on the server
4. If someone wants to access a file using NFS, an RPC call is placed to NFSD (NFS daemon) on the server
	1. This call takes params such as:
		- file handle
		- name of the file to be accessed
		- user's `user ID`
		- user's `group ID`
	2. Used to determine access rights to requested file

### What runs NFS?
NFS is *sortof* platform independent, that is using the NFS protocol, you can transfer files between computers running Windows and other non-Windows operating systems such as Linux, MacOS or UNIX. 

#### E.g.
A computer running Windows Server can act as an NFS File server for other non-Windows client computers.

Likewise a Windows computer running **Windows Server** can access files stored on a non-Windows NFS server. 

### Technical Overview/Links
- https://docs.oracle.com/cd/E19683-01/816-4882/6mb2ipq7l/index.html
- https://www.datto.com/library/what-is-nfs-file-share
- https://wiki.archlinux.org/index.php/NFS

