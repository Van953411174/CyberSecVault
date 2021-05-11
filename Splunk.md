<- [[SIEM]]
# Splunk

[[splunk-quick-reference-guide.pdf]]

A very popular #SIEM, though its not only for security and is used for data analysis, DevOps, etc.

**User Preference Changes**
Edit *user-prefs.conf* to make changes: 
-   Windows: `C:\Program Files\Splunk\etc\apps\user-prefs\default\user-prefs.conf`
-   Linux: `/opt/splunk/etc/apps/user-pref/default/user-prefs.conf`

In order for user preference changes to take effect, the **_splunkd_** has to restarted. 


## Navigating 
https://docs.splunk.com/Documentation/Splunk/8.1.3/SearchTutorial/NavigatingSplunk
Components:
- **Splunk Bar**
	- System level *Messages*, *Settings*, *Activity*, *Help*, and *Find*
- **Apps Panel**
	- Default App for every installation is *Search & Reporting*
	- Clicking the Cog Icon redirects to the *Manage Apps* page where you can change various settings for installed apps.
- **Explore Splunk**
	- quick links to add data to instance, add new apps, access documentation
- **Home Dashboard**
	- By default no dashboards are displaying
	- Select from dropdown or from *dashboards listing page*

## Apps
[Managing App Object](https://docs.splunk.com/Documentation/Splunk/8.1.2/Admin/Managingappobjects)

### Search & Reporting
Where Splunk queries are entered to search the data ingested by Splunk.


## Adding Data
[Use apps to get data in](https://docs.splunk.com/Documentation/Splunk/8.1.2/Data/Getstartedwithgettingdatain#Use_apps_to_get_data_in)
Data ingested by Splunk is processed and transformed in a series of individual events, with data sources being grouped into categories:
- Files and directories
- Network events
- IT Operations
- Cloud services
- Database services
- Security services
- Vitualization Services
- Application services
- Windows sources
- Other sources

**Process**
Add Data via the `Add Data` link from the Splunk Home screen.

There are pre-built guides for *Cloud Computing*, *Networking*, *Operating System*, and *Security*.

However, if those don't work there's also *Upload*, *Monitor*, and *Forward* at the bottom. 

### Adding Sysmon Logs
[[Sysmon]] logs and [[Windows - Event Logs]] can be added via *Monitor* -> *Local Event Logs*.

If #Sysmon and Powershell logs aren't in the list of `Available item(s)`, check `Settings -> Data Inputs` to add available logs. 

## Queries
Splunk uses an *implicit* `AND` between keywords. That is a query like `* GoogleUpdate.exe chrome_installer.exe` is really like `* AND GoogleUpdate.exe AND chrome_installer.exe`. 