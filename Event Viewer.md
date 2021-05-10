<- [[Windows - Event Logs]]
# Event Viewer
An #MMC (Microsoft Management Console) snap-in, Event View can be launched by simply right clicking the #Windows Icon in the taskbar and selecting `Event Viewer`

Also can be launched from CLI with `eventvwr.msc`

Like Registry Editor, you have to manually drill down to get to anything. 

**Layout**
1. Left pane - hierarchical tree listing of event log providers
2. Middle Pane - general overview/summary or events specific to a selected provider
3. Right Pane - actions

## Middle Pane 
A brief explanation for each column for events specific to provider:
-   The first column is **Level**, which is the event type. Recall from earlier there are 5 different event types. This first entry is labeled as **Information**.
-   Next is **Date and Time**, which is when the event was logged.
-   The third column **Source** is the name of the software that logs the event. From the above image, the source is PowerShell.
-   Events are identified by IDs (**Event ID**), which is the fourth column. Note that Event IDs are not unique. Meaning that Event ID 4103 in the above image is related to Executing Pipeline but will have an entirely different meaning in another event log.
-   Lastly is **Task Category**, which is an Event Category. This entry will help you organize events so Event Viewer can filter them. The event source defines this column.

More information is displayed in the split pane as the bottom of the middle pane:
This section has 2 tabs: **General** and **Details**.
-   General is the default view, and the rendered data is displayed.
-   The Details view has 2 options: Friendly view and #XML view.

## Right Pane - Actions
Most useful
- Create Custom View
- Filter Current Log

Basically identical but the latter, as the name implied *only* affects the current log while the former *Create Custom View* affects the global view.