# Sysinternals Suite
#Windows utility suite that can be downloaded or run from the web (https://live.sysinernals.com/). The entire suite is over 70+ tools, but can be broken down by category, or alternatively searched/downloaded via the [index](https://docs.microsoft.com/en-us/sysinternals/downloads/). *NOTE: When downloading the suite, only select tools are included, download [here](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite).*


Links to usage of Suite in specific CyberSec roles(some outdated):
-   Mark's Blog - https://docs.microsoft.com/en-us/archive/blogs/markrussinovich/
-   Windows Blog Archive - https://techcommunity.microsoft.com/t5/windows-blog-archive/bg-p/Windows-Blog-Archive/label-name/Mark%20Russinovich
-   License to Kill: Malware Hunting with Sysinternals Tools - https://www.youtube.com/watch?v=A_TPZxuTzBU 
-   Malware Hunting with Mark Russinovich and the Sysinternals Tools - https://www.youtube.com/watch?v=vW8eAqZyWeo

## Categories
- [[Files and Disk Utilities - Sysint]]
- [[Networking Utilities - Sysint]]
- [[Process Utilities - Sysint]]
- [[Security Utilities - Sysint]]
- [[System Information - Sysint]]
- [[Miscellaneous - Sysint]]

## Local Install
- Once the suite is downloaded and unzipped, you can add the folder path to the environment variable to make the tools accessible from the command line.
*System Properties -> Advanced -> Environment Variables*
![[windows-environment-variables.png]]

- Alternatively you can a #Powershell module can download and install of the Sysinternals tools:
`Download-SysInternalsTools C:\Tools\Sysint`, where the path is location to download/install. 

## Live Sysinternals
To use a tool hosted on https://live.sysinernals.com/, you can either:
1. Input the tools path into Windows Explorer
	1. Ex: `live.sysinternals.com/tools/procmon.exe`
2. Input the tools path into a command prompt:
	1. Ex: `\\live.sysinternals.com\tools\procmon.exe`
	2. Ex from mapped network drive, see [[Windows - Sysinternals Suite#Running Live|running-live-cli]] for graphic : 
		1. Mounts the share: `net use * \\live.sysinternals.com\tools\`
		2. Navigate to tool and run: `procmon`

### Live on CLI
There are some requirements to run the live tools from CLI. 
#### Setup Process
**First**
Before you're able to call tools from the live source via command-line, the *WebDAV* client must be installed and running on the host. 

#WebDAV protocol is what allows a local machine to access a remote machine running a WebDAV share and performs actions in it.

On a Windows 10 client, WebDAV client is likely installed but likely not running. The service will need to be running before you can successfully run a live tool 

##### Installing WebDav
Check the **Starting WebDAV service** section below as WebDAV might already be installed. 

If its not, it can be installed via a Windows feature via PowerShell and requires a restart:
`Install-WindowsFeature WebDAV-Redirector -Restart`

After reboot verfiy the installation:
`Get-WindowsFeature WebDAV-Redirector | Format-Table -Autosize`

##### Starting WebDAV service
To get the service status on PowerShell:
`get-service webclient`
![[powershell-getservice-cmdlet.png]]

To start the service:
`start-service webclient`

**Second**
*Network Discovery* needs to be enabled as well. This setting can be enabled in the *Network and Sharing Center*. 
Click *Change advanced sharing settings* and select *Turn on network discovery*

#### Running Live
There are 2 ways the tools can be run:
1. Run the tool from CLI: : `\\live.sysinternals.com\tools\procmon.exe`
2. Create a network drive and run the tool from the mapped drive
	1. ![sysinternals live mapped drive mount](https://assets.tryhackme.com/additional/sysinternals/win2019-method2a.png)
	2. ![sysinternals live mapped drive nav](https://assets.tryhackme.com/additional/sysinternals/win2019-method2c.png)
	3. ![sysinternals live mapped drive use](https://assets.tryhackme.com/additional/sysinternals/win2019-method2d.png)





## Example Usage of Suite
**Real-world scenario**: As a security engineer, I had to work with vendors to troubleshoot why an agent wasn't responding on an endpoint—the tools used were **ProcExp**, **ProcMon**, and **ProcDump**.

-   ProcExp = to inspect the agent process, its properties, and associated threads and handles.
-   ProcMon = to investigate if there were any indicators on why the agent was not operating as it should.
-   ProcDump = to create a dump of the agent process to send to the vendor for further analysis.