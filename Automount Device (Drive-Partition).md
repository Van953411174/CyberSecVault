# How to Automount Dev (Drive/Partition)
Process of mounting a partition and setting to automount on boot in #Linux. 


## Requirements
Partition identifier, e.g. `/dev/sda6`
UUID of Drive

### Utils and Sys files
- `fdisk`
- `blkid`
- `/etc/fstab`

## Steps
1. Determine partition or drive you want to mount using `fdisk -l`
	1. Record device identifier for reference in Step 3, e.g. `/dev/sda6` 
2. Make a mount point for the drive where ever you want: `mkdir /media/<mntpt>` or `/data`
3. Determine UUID of desired partition using `blkid`
	1. Record UUID, e.g. `/dev/sda6: UUID="280a1509-1b60-425b-9335-1ec445d8f53a"`
4. Edit `/etc/fstab` and add an entry for the device
	1. NOTE: You can technically just use the device name and not the UUID but its safer to use the UUID as it *shouldn't* change but the device name can. 
	2. Format for entries is:![[fstab-entry-format.png]]
		1. See: https://wiki.debian.org/fstab for explanation of *options*
	3. Example entry:![[fstab-entry-example.png]]
5. Test the new mount: `sudo mount -a`
	1. If no errors present themselves, then its **safe** to reboot

