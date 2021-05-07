# ADS 
Alternate Data Streams, or #ADS, is a file attribute specific to #Windows [[NTFS - New Technology File System]] ( #NTFS).

Every file has at least one data stream `$DATA` and ADS allows files to contain more than one stream of data. 

## Forensics
### Tools
Natively Windows Explorer doesn't display ADS to the user. There are 3'rd party programs that can, but #Powershell has the ability to view ADS for files. 
#### Powershell
https://blog.malwarebytes.com/101/2015/07/introduction-to-alternate-data-streams/
- Get ADS streams
	- `get-item -path <path> -stream *`
		- Lists all streams 
- View contents of streams
	- `get-contents -path <path> -stream <stream_name>`
		- View data in stream

Additionally, the Sysinternals tool [[Files and Disk Utilities - Sysint#Streams|Streams]] is designed to reveal NTFS alternate streams. 

### Identifiers
ADS is not malicious in itself and has many legitimate uses. In fact, files downloaded from the Internet to an endpoint have identifiers written to ADS to identify that it was downloaded from the Internet. 

This can allows the system to add additional security measures to its properties. 

*Example of the Sysinternals.zip download*
![[ads-additional-security-internet-download.png]]
### Malware
#malware writers have used ADS to hide data to an endpoints, as well as use them to embed functionality via macros or schedule tasks. 

In fact macros present in #Microsoft Office suite utilize ADS. 



