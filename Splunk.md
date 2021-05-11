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

## Dashboards & Visualizations
*NOTE: Dashboards are App specific, that is if you create a dashboard for the Search App the dashboard uses that app's context*
-   [Visualization Web Framework](https://docs.splunk.com/Documentation/Splunk/8.1.2/Viz/WebFramework)
-   [Visualization Manuall](https://docs.splunk.com/Documentation/Splunk/8.1.2/Viz/Aboutthismanual)
-   [Create Dashboards](https://docs.splunk.com/Documentation/Splunk/8.1.2/Viz/CreateDashboards)
-   [Add Panels](https://docs.splunk.com/Documentation/Splunk/8.1.2/Viz/AddPanels)
-   [Tutorials - Search: Create New Dashboard](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Createnewdashboard)

Visualization Ref and Documentation - https://docs.splunk.com/Documentation/Splunk/8.1.2/Viz/Visualizationreference

Navigate to *Dashboards* in the Menu Bar for the Desired APP

### Example - Top 5 Sysmon Event IDs

![[splunk-dashboard-example.gif]]

Search: `source="XmlWinEventLog:Microsoft-WIndows-Sysmon/Operational" | top limit=5 EventID`

Select your Visualization and save to *Dashboard Panel*, and if the dashboard is already created, save to *Existing* and select your dashboard. 

Your dashboard should now be populated.

You can set to Home Dashboard via the "..." menu top left -> Set as Home Dashboard. 

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
-   [About The Search App](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Aboutthesearchapp)
-   [Start Searching](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Startsearching)
-   [Time Range PIcker](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Aboutthetimerangepicker)[](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Aboutthetimerangepicker)
-   [Useful Fields to Search](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Usefieldstosearch)[](https://docs.splunk.com/Documentation/Splunk/8.0.4/SearchTutorial/Usefieldstosearch)
-   [Useful Field Lookups](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Usefieldlookups)
-   [Search With Field Lookups](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Searchwithfieldlookups)[](https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/Searchwithfieldlookups)
-   [Regular Expressions](https://docs.splunk.com/Documentation/Splunk/8.1.2/Knowledge/AboutSplunkregularexpressions)


Splunk uses an *implicit* `AND` between keywords. That is a query like `* GoogleUpdate.exe chrome_installer.exe` is really like `* AND GoogleUpdate.exe AND chrome_installer.exe`. 
## Alerts
*NOTE: In Splunk Free Alerts are not available*

Alert Docs: https://docs.splunk.com/Documentation/Splunk/latest/Alert/Aboutalerts
Alert Examples: https://docs.splunk.com/Documentation/SplunkCloud/8.1.2012/Alert/Alertexamples

Alerts enable us to monitor and respond to specific events. Alerts use a saved search to monitor events in real-time or on a schedule. They'll trigger when a specific condition is met to take the defined course of action. 

### Alert Flow
[The alerting workflow](https://docs.splunk.com/Documentation/SplunkCloud/8.1.2012/Alert/AlertWorkflowOverview)
**Search: What do you want to track?** 
-   There is an external IP brute-forcing a web page. We want to be alerted whenever this IP address is actively attacking the infrastructure. 

**Alert Type: How often do you want to check for events?**
-   Since we want to be alerted whenever this IP is active, a real-time alert is what we'll configure. 

**Alert trigger conditions and throttling: How often do you want to trigger an alert?**
-   If 10 failed password events under 1 minute, generate an alert. 

 **Alert action: What happens when the alert triggers?**
-   Send an email or send a message in an application using a [webhook](https://docs.splunk.com/Documentation/Splunk/8.1.2/Alert/Webhooks).