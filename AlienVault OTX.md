# AlienVault OTX
My acct under: sec.070.fc gmail

AlienVault OTX (see [here](https://otx.alienvault.com/)) is an [[ISAC]] from AT&T Cybersecurity, and is one of the main #ISAC  that is used as an exchange for community maintained [[Cyber Threat Intelligence]]. 

It uses 'Pulses' to create trackers for various categories of #threat_intelligence. Pulses can be categorized by #malware type, #APT  or group, and targeted industry, and can include a wide variety of #IOC such as IPs, hashes, domains, URLs, #YARA, CVE, and more. All pulses are community-created excluding official pulses from #AlienVault. 

## Navigation and UI
The UI consists of 6 different menu tabs above the default dashboard. These are:
-   Dashboard - This is shown above in the screenshot above. It's the main page of OTX and will provide a brief overview of important intel.
-   Browse - This will allow you to see all new pulses and sort by various categories to find the newest intel.
-   Scan Endpoints - This is an automated service called OTX Endpoint Security that will scan endpoints for indicators.
-   Create Pulse - This will allow you to create your own pulses and contribute to the threat exchange.
-   Submit Sample - This allows you to submit a malware sample or URL sample which OTX will analyze and generate a report based on the provided sample.
-   API Integration - Allows synchronization of the threat exchange with other tools for monitoring your environment.


### Dashboard
Main page is the default dashboard, which includes visualizations of the most commont active malware broken down by category as well as a list of Subscribed Pulses (i.e. what you want to follow). *By default only AlienVaults's Subscribed Pulses will be listed.*

## Pulses
Can consist of a description, tags, indicator types (file hash, Yara, IP, domain, etc.), and threat infrastructure (country of origin). OTX uses pulses as their indicators. A majority of pulses are community-created and maintained. You need to keep this in mind when using pulses for #TI as **not all pulses are legit** or may contain inaccurate information. Always verify and analyze the indicators used before using them for #CTI.
 Pulses consists of three main sections:
 - **Pulse Description**
	 - description
		 - what the pulse is and how it was gathered
	 - references
	 - tags
	 - malware families
	 - #ATTCK IDs
		 - [[ATT&CK Framework]] IDs used to quickly identify what #TTP are being used by the pulse
 - **Indicator Overview**
	 - Brief statistical representation of the indicators w/i the pulse + threat infrastructure
		 - *A majority of pulses do not have threat infrastructure*
	 - Useful when looking for specific IOC
	 - Ex: ![Indicator Overview](https://i.imgur.com/J4zKnQB.png)
 - **Indicators**
	 - Contains all the indicators and information about them, most important part of a pulse
	 - Information is broken down by columns:
		 -  **Type** - The type of indicator (URL, File Hash, IP, Domain, etc.)
		 -   **Indicator** - The indicator itself
		 -   **Added** - Date added, pulses can be updated this can be useful to track the pulses history
		 -   **Active** - Shows, whether the indicator is still seen in the wild and active, can be useful when selecting pulses to use.
		 -  **Related Pulses** - Shows pulses that share the same indicator, can be useful to cross-check indicators.
		 -   **Extra Information (Advanced)** - These are the advanced options including Dynamic Analysis, Network Activity, and YARA rules.
			 -   Contains the most information

### Finding Pulses
Finding the right pulses is half the battle. Thankfully, AlienVault allows you to easily find and filter pulses by an extensive list of attributes.
#### by Malware
If you're interested in a specific piece of malware, you can find a pulse for that. This can allow you to quickly find IOC and rules for a specific strain of malware. *Just be aware that malware constantly changes and that indicators change when looking for specific malware*
![Example of malware-specific Pulse](https://i.imgur.com/LVvBTDG.png)

The menu for malware is by far the most detailed including features of the malware, related pulses, process visualization, and file samples if available. OTX will also visualize the processes the malware is running. Overall the malware categories will allow you to quickly identify multiple aspects of malware including processes, features and pulses.

![Malware execution visualization](https://i.imgur.com/ZgWoLk0.png)  

#### by Adversary
Requires foundational knowledge of adversaries and their operations, but allows you to identify pulses based on adversary group. The adversary menu gives you a short description of the group as well as the pulses related to that group.
#### by Industry
Industry is always a common vector for adversaries to focus on. Find industry-specific pulses can enable you to stay on top of threats related to your industry. The industry menu gives a general description as well as any pulse tag related to the industry, such as a *fraud* tag for finance. 


### Creating Pulses
Accomplished through the *Create Pulse Tab*. AlienVault OTX can automatically extract indicators including MD5, SHA256, etc. *Even though* OTX automatically extracts it is still suggested to create your own IOCs.

OTX also has built-in functionality to take a malware sample under the *Submit a sample* tab, and analyze it. This will give back indicators as well as related pulses. 

