# Windows - Event Logs
#Windows Event Logs are not text files but raw data that can be translated into XML using the Windows API.  The events stored in these log files are stored in a proprietary binary format with a *.evt* or *.evtx* extension, the latter typically residing in `C:\Windows\System32\winevt\Logs`

## Types of Events and Logs

### Events
There are [5 types](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-types) of events that can be logged. Brief table follow with expanded descriptions in link. 

| Event Types   | Description                                           |
| ------------- | ----------------------------------------------------- |
| Error         | significant problem such as loss of data/functionality |
| Warning       | not significant but could cause future problems       |
| Information   | successful operation of app/driver/service            |
| Success Audit | successful security access                            |
| Failure Audit | failed security access                                                      |

### Logs
There are [4 types](https://docs.microsoft.com/en-us/windows/win32/eventlog/eventlog-key) of Windows Logs. As above, brief table follows:

| Log Type     | Description                                                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| Application  | Events logged by applications                                                                                                   |
| Security     | Events such as valid/invalid login attempts, as well a events related to resource use (creating/opening/deleting files or objs) |
| System       | Events logged by system components, such as the failure of a driver or other system component                                   |
| *Custom Log* | Events logged by applications that create a custom logs                                                                                                                                |

For example, #PowerShell (an application), will log operations from the engine, providers, and cmdlets to the Windows event log. 

## Methods of Access
Three Main ways of accessing event logs within a Windows system:
1.  **Event Viewer** (GUI-based application)
2.  **Wevtutil.exe** (command-line tool)
3.  **Get-WinEvent** (PowerShell cmdlet)

### Event Viewer
An #MMC (Microsoft Management Console) snap-in, Event View can be launched by simply right clicking the Windows Icon in the taskbar and selecting `Event Viewer`

Also can be launched from CLI with `eventvwr.msc`

Like Registry Editor, you have to manually drill down to get to anything. 

**Layout**
1. Left pane - hierarchical tree listing of event log providers
2. Middle Pane - general overview/summary or events specific to a selected provider
3. Right Pane - actions

#### Middle Pane 
A brief explanation for each column for events specific to provider:
-   The first column is **Level**, which is the event type. Recall from earlier there are 5 different event types. This first entry is labeled as **Information**.
-   Next is **Date and Time**, which is when the event was logged.
-   The third column **Source** is the name of the software that logs the event. From the above image, the source is PowerShell.
-   Events are identified by IDs (**Event ID**), which is the fourth column. Note that Event IDs are not unique. Meaning that Event ID 4103 in the above image is related to Executing Pipeline but will have an entirely different meaning in another event log.
-   Lastly is **Task Category**, which is an Event Category. This entry will help you organize events so Event Viewer can filter them. The event source defines this column.

More information is displayed in the split pane as the bottom of the middle pane:
This section has 2 tabs: **General** and **Details**.
-   General is the default view, and the rendered data is displayed.
-   The Details view has 2 options: Friendly view and XML view.

#### Right Pane - Actions
Most useful
- Create Custom View
- Filter Current Log

Basically identical but the latter, as the name implied *only* affects the current log while the former *Create Custom View* affects the global view. 

### wetvutil.exe
Microsoft docs: https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil
Enables you to retrieve information about event logs and publishers. You can also use this command to install and uninstall event manifests, to run queries, and to export, archive, and clear logs.

To pass a path to a log file to a subcommand, use the following format
`wevtutil <subcmd> <PATH-to-log> /lf:true`
Usage
![[wevtutil_exe-usage.png]]
Usage sub commands
![[wevtutil_exe-usage-commands.png]]
Some common options (many more)
![[wevtutil_exe-common-options.png]]


### Get-WinEvent