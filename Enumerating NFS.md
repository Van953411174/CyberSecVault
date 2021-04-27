# Enumerating NFS
Being a network file system, #NFS is a ripe target for #enumeration. 
Sensitive data, user profiles, network topology, etc can be discovered from NFS. 

## Required Tools
- nfs-common
	- installed either as client or server
		- inlcudes programs such as:
			- lockd
			- statd
			- showmount
			- nfsstat
			- gssd
			- idmapd
			- mount.nfs

## Process
Start with a #port_scan via #nmap or some other tool.
`nmap -p- -A -vv IP`

To access an NFS share you need a directory to mount to, this can be anywhere on the system, e.g. `mkdir /tmp/mount`


Example nmap scan indicating NFS
![[Pasted image 20210427134628.png]]
- `nfs_acl` is the endpoint to mount

List the available shares with:
`showmount -e IP`
- `-e` show NFS server's export list


Next mount to remote share to the directory
`sudo mount -t nfs IP:share /tmp/mount -nolock`
- `-t nfs` type of device to mount
- `-nolock` specifies not to use NLM locking
	- #NLM - Network Lock Manager, an #RPC protocol
		- file record locking enables clients to synchronize I/O request with other clients ensuring data integrity
		- When an NFS server grants a lock to a client it must maintain a record of the client that owns the lock
	- #NSM - Network Status Monitor, an #RPC protocol
		- notifies clients of loss of lock state due to server reboot

At this point you should be able to navigate the mounted share like a local directory. 

See [[Exploiting NFS]] for more.