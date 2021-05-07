# Sysinternals - Files and Disk Utilities
#Windows [[Windows - Sysinternals Suite|Sysinternals Suite]] category covering Files and Disk Utilities. This note is **non-exhaustive**

Like to full category tools: https://docs.microsoft.com/en-us/sysinternals/downloads/file-and-disk-utilities

## Sigcheck
> "**Sigcheck** is a command-line utility that shows file version number, timestamp information, and digital signature details, including certificate chains. It also includes an option to check a file’s status on VirusTotal, a site that performs automated file scanning against over 40 antivirus engines, and an option to upload a file for scanning."

\- Sigcheck [page](https://docs.microsoft.com/en-us/sysinternals/downloads/sigcheck)

### Use Cases:
1. Check for unsigned files in `C:\Windows\System32`, as all files there should be signed. 
	1. `sigcheck -u -e C:\Windows\System32`
		1. `-u` if VirusTotal check is enabled, show files that are unknown by VirusTotal or have non-zero detection, otherwise show only unsigned files
		2. `-e` Scan executable images only (regardless of their extension)
	2. Any results *other* than `No matching files were found` warrents further investigation. 

## Streams
> "The NTFS file system provides applications the ability to create alternate data streams of information. By default, all data is stored in a file's main unnamed data stream, but by using the syntax 'file:stream', you are able to read and write to alternates."

\- Streams [page](https://docs.microsoft.com/en-us/sysinternals/downloads/streams)

[[ADS- Alternate Data Streams]], #ADS is a file attribute specific to Windows [[NTFS - New Technology File System]] #NTFS. 

Example of added identifier to downloaded file: 
![[streams-sysinternals-added-identifier-download.png]]


## SDelete
> "**SDelete** is a command line utility that takes a number of options. In any given use, it allows you to delete one or more files and/or directories, or to cleanse the free space on a logical disk."

\- SDelete [page](https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete)

SDelete, or Secure Delete, is used for secure file deletion and is part of the [[DoD 5220.22]]-M (DoD data clearing and sanitizing protocol)

SDelete has been used by #threat_actors and is associated with [[ATT&CK Framework]]:
- MITRE ID [S0195](https://attack.mitre.org/software/S0195/).
-  Techniques
	- [T1485](https://attack.mitre.org/techniques/T1485/) (**Data Destruction**)
	- [T1070.004](https://attack.mitre.org/techniques/T1070/004/) (**Indicator Removal on Host: File Deletion**).