# Nessus
#Nessus is a #vulnerability_scanner and uses techniques similar to #nmap to find and report vulnerabilities, which are then presented in a nice GUI.

Nessus doesn't make assumptions like other scanners while scanning, like assuming a web application is running on port 80.

Like other vulnerability scanners Nessus scans can be scheduled, allowing for #continuous_monitoring , and its #reporting abilities make it a useful tool in #vulnerability_management.

## Starting Nessus
To start Nessus you need to start the daemon service:
`sudo systemctl start nessusd.service`

After the service starts, navigate to `https://localhost:8834`, ignore the security warning (its just because the certificate is self-signed)

Credentials: `nessy-admin::Findlochnessy`

## Config & Options
### Scan Templates
Clicking *Create a new scan* or the **New Scan** button will open up the Scan Templates page, where you can select the scan you want, fill out the relevant options, and Save it (or alternatively Launch it from the drop down on Save)
![[Pasted image 20210430002933.png]]

#### Custom Templates (Policies)
You can create custom templates defining what actions are performed during a scan under _Policies_ in the Side Menu. Once created the template can be found with the other Scan Templates. 

### Plugin Properties
You can change plugin properties under _Plugin Rules_ such as hiding them or changing their severity. They can be limited to a specific host or specific time frame as well.

## Common Scan Types
There are a number of scan templates as shown in the image above
Some common ones:
- Host Discovery
	- what hosts are on the network and live
- Basic Network Scan
	- fully system scan suitable for any host
- Credentialed Patch Audit
	- Authenticate to hosts and enumerate missing updates
- Web Application Test
	- Scan for published and unknown web vulnerabilities

## Running a Scan
Basic Network Scan example.
![[Pasted image 20210430004753.png]]

### Settings[^1]
Its important to note that you can **Schedule** a scan under the Settings -> Basic -> Schedule. 
![[Pasted image 20210430005044.png]]

For Basic Network Scan the requires fields are *Name* and *Targets*. 
![[Pasted image 20210430005322.png]]

**Discovery** settings lets you set how the scanner discovers hosts. 
![[Pasted image 20210430005613.png]]

**Assessment** allows you to configure how the scanner assesses each host for vulnerabilities
![[Pasted image 20210430005919.png]]

**Report** allows configuration of scan output and formatting
![[Pasted image 20210430010027.png]]
**Advanced**  lets you quickly set the scan for low-bandwidth
![[Pasted image 20210430010154.png]]

Once you have all the desired setting *Save* and Launch!

On completion, and while running, Vulns will populate with discoveries. 
![[Pasted image 20210430011657.png]]
![[Pasted image 20210430011721.png]]


[^1]:Its worth noting that all of the settings allow for a *Custom* option that drastically expands the settings available outside of the quick options. Example: Advanced -> Custom allows for filepath exclusion, debug settings, max and min connections, etc. 

## Closing Nessus
The service must be manually shutdown if you do not want it continuing to run in the background. This can be accomplished with:
`sudo systemctl stop nessusd.service`

