Adware/PuP
drive by downloads
commodity malware
phishing
HoK (hands on keyboard)


Valek is phishing
- weakest link
- wide variety of attacks
	- steals creds
	- malicious attachments
	- social engineering

Its a modular, multi-stage malware discovered in December 2019
- evolved from loader into an info
- ~30 different variants over 6 months
- Microsoft word docs embedded with malicious macro code
- Mainly english and german


Two Stage 
- loader
	- sets itself up
- installtion
	- persistence

Uses ADSs (alternate data streams) for storage and scheduled tasks for persistence
- Attribute of NTFS
- Can be used to hide the presenece of a secret or malicious fil einside the file record of an innocent file
- Ex. Zone.identifier 
	- helps pinpoint where a file was downloaded from
- 
Network communications are conducted using HTTP to contract hard-coded, C2

Stages:
- Loader
	- Embedded word doc
		- enables malicious dll
			- dll ran via regsvr32.exe
				- drops js & downloads js payloads
					- stores C2 info in registry
					- creates schedule task


==If the C2 info is hardcoded, do you simply blacklist the C2 for outbound/inbound to isolate it?==

Scheduled task for persistence
- c2 info in register and ads js in ran  


Analysis Demo
- FlareVM
- Download file
- Unzip
- Open process monitor
- Execute word doc
- what what it does

olevba -  python script that analyzes word docs with embedded macros

Malware traffic analysis.net

