# OpenVAS 
#OpenVAS, or Open Vulnerability Assessment Scanner, i.e. a #vulnerability_scanner, is an application to scan endpoints and web applications to identify and detect vulnerabilities. Commonly used as part of mitigation solutions to quickly identify any gaps in production or development servers/applications. #vulnerability_management

Is part of the Greenbone Vulnerability Management ( #GVM) Solution. Used for GreenBone Security Manager appliances and is a full-featured scan engine that executes a continuously updated and extended feed of Network Vulnerability Tests ( #NVT)

- See [[GVM - Greenbone Vulnerability Management]] for more on the framework. 
- See https://docs.greenbone.net/ for Documentation

**Sample Report**:* Media/sample-openvas-report.html*
*Obsidian does not support html or iframes referencing local html files, sad face :( *

-------------
**_NOTE_**: OpenVAS renamed to `gvm` in 2020. Some distros reference OpenVAS with `openvas` or `gvm` or a combination of both.

----------------

## Installation Options
OpenVAS comes preinstalled on Kali and Parrot, but Docker containers for it are also available.
### via Docker
To Run via #Docker: `docker run -d -p 443:443 --name openvas mikesplain/openvas`
https://github.com/mikesplain/openvas-docker

This will pull the docker container (**INTERNET required**) and then run the container. Obviously you can download the container yourself and run it without pulling from repo. 

Once running, navigate to `https://127.0.0.1` in your web browser to access the OpenVAS interface. 

Default credentials are *admin::admin*

To close simple run `docker stop openvas`

### via Installed Package
With OpenVAS installed by default on Parrot/Kali, you can also easily start the application from the command line.
 - `gvm-setup` - setup script
 - `gvm-feed-update` - signature updater
 - `gvmd` - Manager of the OpenVAS system
 - `gvm-manage-certs` - Manage cert infra for an OpenVAS installation

There are two additional utils w/ Parrot:
- `openvas-nasl` Executes a set of #NASL ( #Nessus Attack Scripting Language) scripts against a given target host.
- `openvas-nasl-lint` Linter for testing scripts for syntax errors

#### Setup
Before use you have to run the setup script. Its **_very_** long process, and an automatically generated password will be displayed for you. 
*SAVE SOMEWHERE SAFE*

##### Errors
It is likely that first time setup you'll encounter an error about #PostgreSQL version.
![[gvm-setup-error-postgres.png]]

This can be remediated rather easily, as odds are you do have the required version but older versions are in the way. 

1. First list out the current clusters w/: `pg_lsclusters` 
![[postgresql-ls-clusters.png]]
2. Next stop the running clusters with a version less than 13: [^1]
 `sudo systemctl stop postgresql@11-main && sudo systemctl stop postgresql@12-main` ![[postgresql-ls-clusters-after-stop.png]]
3. Then remove the old clusters, i.e. drop them: `sudo pg_dropcluster 11 main && sudo pg_dropcluster 12 main`
4. Lastly, verify the clusters have been removed (`pg_lsclusters`) and rerun *setup* (`sudo gvm-setup`. ![[pg-ls-clusters-after-drop.png]]

[^1]: Technically this can be skipped by using the `--stop` option with `sudo pg_dropcluster`. However its more graceful to use `systemctl` ahead of time. 

After setup completion, or after `gvm-start` there will be two listening TCP ports: 9390 and 9392, with the latter being the Web Interface accessible at:
`https://127.0.0.1:9392`

#### Resetting Password
The `gvmd` util can be used to create and remove users as well as reset their passwords. 

Example: 
```
root@kali:~# gvmmd --create-user=dookie  
User created with password 'yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyy'.  
root@kali:~# gvmmd --get-users  
admin  
dookie  
root@kali:~# gvmmd --user=dookie --new-password=s3cr3t  
root@kali:~# gvmmd --user=admin --new-password=sup3rs3cr3t
```

## Using OpenVAS
### Tasks
Tasks are exactly what they sound like, jobs for OpenVAS to carry out that can be scheduled, queued, and analyzed. 

#### Create a Task
1. Under *Scans -> Tasks*, click the ![[openvas-wizard-task.png]]for the *Task Wizard* or the ![[openvas-star-icon.png]]to create a *New Task*


##### Clicking *New Task*
After clicking ![[openvas-star-icon.png]] and *New Task*, a creation pane comes up. 

![[openvas-task-creation-pane.png]]

Once you've set everything click *Create* to create the task. 

##### Scanner
By default the OpenVAS scanner is used though this can be configured. 
##### Targets
To set your *Scan Targets* either select a previously added Target or click the ![[openvas-star-icon.png]] new to *Targets* to open the *New Target* pane
![[openvas-new-target-pane.png]]

#### Running a Task
Once a task is created it will show up in the *Tasks* pane with its status and actions. 

![[openvas-tasks-pane.png]]

From here you can Start, Stop, Trash, Edit, Clone, or Export the task.

**Starting**
Once you start a task its status will updated to *Requested* as the task is carried out. Clicking on the status will redirect you to the report being populated for the scan. 
![[openvas-run-task.png]]

**Completed**
Once the scan is completed, the total *Tasks* pane will update to reflect the findings, as well as the attacked details to the scan (Total Reports, Severity)

![[openvas-tasks-pane-completed.png]]

Clicking on the *Done* status will open the Report of the scan, where the most Severe vulnerabilites are displayed first, and further clicking on the findings will display more information about the discovered vulnerability.
![[openvas-task-report.png]]


### Reporting and Monitoring
A scanner is nothing without the ability to generate reports. OpenVAS has great automated #reporting capabilities, as well the ability for scheduled or #continuous_monitoring. 

#### Continuous Monitoring
Continuous scanning utilities in OpenVAS include Alerts, Schedules, and Agents.
##### Schedules
Schedules are attacked to tasks to dictate when they should run, rather than a schedule being used to pick and choose when various tasks are run. This was you could say create a schedule for weekly, biweekly, and monthly, and attach them to different task groups to dictate those tasks should run. 

Once a schedule is created it can be selected in the *Schedule* drop-down menu when creating a *New Task* | [[OpenVAS#Create a Task]]

**Creating Schedules**
To create a schedule navigate to *Configuration -> Schedules*, then click the ![[openvas-star-icon.png]] to open the *New Schedule* pane. 
![[openvas-new-schedule.png]]

##### Alerts
Alerts are automated messages emailed to a monitoring account generated off an event and condition. 

Like Schedules, Alerts are attached to task when editing or creating them | [[OpenVAS#Create a Task]]

**Creating Alerts**
Just like schedules, navigate to *Configuration -> Alerts* and click the ![[openvas-star-icon.png]] to open the *New Alert* pane
![[openvas-new-alert.png]]

The main options to worry about are:
- *Name*
	- Descriptive name for Alert
- *Event*
	- can be configured to alert based on the status of the scan or when a new #NVT / #vulnerability is detected
- *Condition*
	- makes sure that your inbox isn't flooded w/ alerts
	- can be changed base on severity or filters
- *To Address*
	- alert will send an email to the specified mail address

##### Agents
An agent is a tool that is installed on a target system. During a Scan task, the agent is contacted by the scanner and ordered to do scans and reports. For one this offloads the scanning overhead to the target machine and also allows for lower-lvl scans. 

Example:  An agent could be a utility on a Windows system to scan the host system for weak passwords and report back to OpenVAS with its findings. 

**Creating Agents**
Navigate to *Configuration -> Agents* and click the ![[openvas-star-icon.png]] to open the *New Agent* pane. 
![[openvas-new-agent.png]]

#### Reporting
**Report Formats**
OpenVAS has the ability to export findings into 15 different report formats, all of which can be customized under *Configuration -> Report Formats*

**List Reports**
You can list all reports and a breakdown on findings by navigating to *Scans -> Reports*
![[openvas-all-reports.png]]

**Import Reports**
You can also import compatible reports to view them. Under *Scans->Reports*, click the **Up** arrow top left to import. 

**Export Reports**
Obviously any report can be exported. 
1. When viewing a report, in the top left hand corner, select the export format (which can be changed)
2. click *Done* button, which will generate a present a download page for Full and Filtered Reports.
	1. The download format can be changed here if desired. 

Alternatively, you can click the **Down** arrow top left to download the filtered report directly in the specified format.