<-[[RangeForce - SOC Detection Challenges Index]]
# RF - Email Detection Challenge

Index:
1. [[RF - Email Detection Challenge#Google Account|Google Account]]
2. [[RF - Email Detection Challenge#Invoice Due Soon|Invoice Due Soon]]
3. [[RF - Email Detection Challenge#Account Hacked|Account Hacked]]
4. [[RF - Email Detection Challenge#Urgent|Urgent]]
5. [[RF - Email Detection Challenge#Helpdesk Request|Helpdesk Request]]
6. [[RF - Email Detection Challenge#New App Connected|New App Connected]]


## Google Account
Using Mozilla Thunderbird and any other necessary tools, answer the following:

**What is the emails _IP_ address of the _origin_?**
`69.168.97.48` 

- Process
	- Open the Email in ThunderBird. 
	- Viewing the Source doesn't provide any information for the GoogleAccount email IP origin. 
		- That information is inside the *GoogleAccount.eml* encoded attachment, which is a copy of the suspicious email forwarded
	- Opening the attachment in Notepad allowed me to view the un-encoded data and the payload of a phishing webpage. 
		- This can also be done in IE or anything that provides text representation
	- Within that un-encoded data was the IP address of the actual origin ![[RF-Email-Detection-Challenge-GoogleAccount-IP-Origin.png]]
	
**According to [ipinfo.io](https://ipinfo.io) , what is the the origin IP's *company name*?**
`Synacor, Inc.`

- Process
	- Very straightforward, use the provided link to search the IP found in the previous question to find the associated company name ![[RF-Email-Detection-Challenge-Company-Name.png]]
	
**According to[ G Suite Toolbox](https://toolbox.googleapps.com/apps/messageheader), what is the *SPF* status?**
`softfail`

- Process
	- Copy the header from the attached encoded email from the first question into the G Suite Toolbox tool for analyzing message header ![[RF-Email-Detection-Challenge-GoogleAccount_eml-header.png]]
	- The tool returns the SPF status (Sender Policy Framework, used to prevent spoofing) ![[RF-Email-Detection-Challenge-SPF-Status.png]]
	
**Which *URL* stands out as malicious in this email?**
`http://router-0d3fa93d-6f83-40bc-a269-eecc9bb0bdc9.eastus.cloudapp.azure.com`

- Process
	- Using just whats provided (and not something like strings or equiv), re-examine the GoogleAccount.eml in Notepad or equivalent
	- Using *Find* and searching for *http* to locate all URLs, you quickly find one to a random Azure instance ![[RF-Email-Detection-Challenge-sus-url.png]]
	
**If you scan the malicious URL with [urlscan.io](https://urlscan.io), what *type* of attack is this?**
`phishing`

- Process
	- Same process as using *ipinfo.io*, copy the URL from the previous question into *urlinfo.io* and hit the green *play* button on the right. 
	- The Home page returned indicates that the domain has *Malicious Activity* indicating it could be *Potentially Malicious* clicking on the *Behaviour* tab states that the URL is **Phishing against: Google** ![[RF-Email-Detection-Challnege-urlscan_io-phishing-detection.png]]

## Invoice Due Soon
> Your accounting department got an invoice from an unknown company, luckily they forwarded the email to you for double-checking before they opened the attachment.

**What is the _IP_ address that delivered the email to your email server?**
`153.153.62.99`

- Process
	- Open the attached email in notepad and navigate to the *Received:* headers.
	- The first IP listed is the loopback local IP from the computer forwarding it to the application
	- The **second** IP is from the host that delivered the email to the email server. ![[RF-Email-Detection-Challenge-Invoice-IP-of-host-to-email-server.png]]

**According to [MX Toolbox](https://mxtoolbox.com/EmailHeaders.aspx), what is the _SPF Alignment_ result?**
`Domain not found in SPF`

- Process
	- Copy the email source from Notepad into MX and tell it to analyze.
	- Scroll down to *SPF and DKIM Information*
	- In the *spf:sasayu.jp* section, scroll down to *Name	SPF Alignment* preceded by a red X. ![[RF-Email-Detection-Challenge-Invoice-MXToolbox-SPF-Alignment.png]]
	
**According to [VirusTotal](https://www.virustotal.com/gui/) in the categorization tags at the top of the results page, which scripting language is used by this malware?**
`Javascript`

- Process
	- I saved the PDF attachment to Downloads, then uploaded to VirusTotal
	- Navigating to Community shows two YARA matches from THOR, indicating embedded Javascript ![[RF-Email-Detection-Challenge-VirusTotal-scripting-language.png]]


## Account Hacked
> Lana Banana got a threatening letter saying her account has been hacked. Help her determine is that really the case.

**What is the origin _"Received: from"_ FQDN?**
*FQDN -> Fully Qualified Domain Name, comprised of hostname and domain name.*
`serv1.youarehackzed.ga`

- Process
	- Open the email source view, and navigate to the *Received: from* field. The following value is the FQDN ![[RF-Email-Detection-Challenge-AccountHacked-FQDN.png]]


## Urgent
> Mark Bark got a Bitcoin demand letter from someone, you need to analyze the email to see if the threat is real.

**According to [MX Toolbox](https://mxtoolbox.com/Public/Tools/EmailHeaders.aspx), *how long* did it take for this email to arrive?**
`24364800 seconds`

- Process
	- Upload email source to MX Toolbox for header analyze, after which the delay will be displayed ![[RF-Email-Detection-Urgent-MXTool-delay-time.png]]


**What is the *IP* address that delivered the email to your email server?**
`133.242.140.203`

- Process
	- Examine either the message source and *Received: from* field OR the results from MX Tool ![[RF-Email-Detection-IP-host-to-email-server.png]]


## FedEx Billing
>The accounting department got an email that looks like it came from FedEx. What are the signs that the FedEx Billing email is not what it appears to be?

**What is the first "_Received: from" IP_ address of `Fedex_Billing.eml`?**
`135.101.115.149`

- Process 
	- Open the Email source and examine the *Received: from* fields ![[RF-Email-Detection-Challenge-FedEx-IP.png]]

**According to [MX Toolbox](https://mxtoolbox.com/SuperTool.aspx?a) SuperTool, what is the _actual_ FedEx mail server _IP_ address?**
`204.135.242.200`

- Process
	- Using SuperTool, look up either FedEx.com or the sender address of *nds.fedex.com*
	- The question isn't asking for the *smtp* server but the load balancer *mapper.gslb.fedex.com* ![[RF-Email-Detection-Challenge-email-server-fedex.png]]


**If you scan the attached file with [VirusTotal], which _CVE_ is being exploited?**
`CVE-2010-3333` 

- Process
	- Upload the attachment to VirusTotal, and the CVE is listed in some of the descriptions ![[RF-Email-Detection-Challenge-CVE.png]]


## Helpdesk Request
> Tim Tram forwarded you an email where the helpdesk appears to be asking his credentials for. Company policy is not to give out your credentials to anybody, thus Tim is suspicious.

**The attached `Helpdesk_Request.eml` email appears to come from helpdesk@contoso.com, what is the message *origin IP*?
`117.4.117.46`

- Process
	- Open email source within Thunderbird and examine *Received: from* field ![[RF-Email-Detection-Challenge-origin-IP.png]]

**If you *reply to* this email, who will actually receive it?**
`helpdesk@contozo.com`

- Process
	- Examining the source and the *Reply-To:* field shows that they're attempting to trick with a homoglyph (*contozo != contoso*) ![[RF-Email-Detection-Challenge-reply-to-homoglyph.png]]

**According to [G Suite Toolbox](https://toolbox.googleapps.com/apps/messageheader/analyzeheader), what is the result of "*delivered after*"?**
`7 hours`

- Process 
	- Copy the message source into the Message Header analysis tool, answer is in *Created at:* field ![[RF-Email-Detection-Challenge-delivered-time.png]]



## New App Connected
> Joe King forwarded you an email from Microsoft that says something about a new app connection. Is this a legitimate email or should Joe worry?

**What is the first "Received: from" IP address of *'New_app_connected_to_your_Microsoft_account.eml*'?**
`65.52.110.208`
- Process
	- Examining the message source for *sender ip is ...* in *ARC-AUthentication-Results:* reveals the IP. ![[RF-Email-Detection-Challenge-NewApp-IP-source-ARC.png]]

**According to [ipinfo.io](https://ipinfo.io), what is the the origin IP's *company name*?**
`Microsoft Corporation`

- Process
	- Simply put the IP from the previous question into *ipinfo.io* ![[RF-Email-Detection-company-name.png]]


**According to [MX Toolbox](https://mxtoolbox.com/Public/Tools/EmailHeaders.aspx), what is the result of *SPF Syntax Check*?**
`The record is valid`

- Process
	- Copy the header into the MX Toolbox, then expand the SPF section and scroll down to the *SPF Syntax Check* ![[RT-Email-Detection-Challenge-NewApp-SPF-Syntax-Check.png]]

**According to [MX Toolbox], what is the result of *DKIM Syntax Check*?**
`The record is valid`

- Process
	- Repeat from previous question but for *DKIM* ![[RF-Email-Detection-Challenge-NewApp-DKIM-Syntax-Check.png]]