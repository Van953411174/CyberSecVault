[[Windows - Event Logs]]
# XPath Query
#XPath, or #XML Path Language, supported by #Windows Event Logs, at least a subset is ([XPath 1.0](https://www.w3.org/TR/1999/REC-xpath-19991116/). 

Both [[wevtutil.exe]] and [[Get-WinEvent]] support XPath queries as event filters

## Constructing Query
An XPath event query starts with `*` or `Event`, and its best practice to explicitly use a keyword following the top-lvl, but can be substituted with `*` like with Event.
- `'*/System'` or `*/*`

Using the Event Viewer, you can select an event, then in the bottom split pane select *Details* an *XML View*. This will show the structure of the event in XML and assist in query creation. 

![[event-viewer-details-xml-view.png]]

### Event ID Query
Building the query just involves understanding XML and stepping through the structure. From the ==highlighted== portions in the image, you can see the important points for constructing a basic query for an event ID. 
![[event-viewer-xpath-query-eventid.png]]

Using *Event*, *System*, and *EventID=326* we can construct and XPath query for Get-WinEvent
```powershell
Get-WinEvent -LogName Application -FilterXPath 'Event/System/EventID=326'

// The following using '*' is also valid
Get-WinEvent -LogName Application -FilterXPath '*/*/EventID=326'
```

### Provider Name Query
Different elements might require slightly different query syntax. For example `Provider Name` will require using the `Name` attribute of the provider. 
![[event-viewer-xpath-query-providername.png]]

The format for the XPath query would be:
```powershell
Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="ESENT"]'
```

### EventData Query
*NOTE: The `EventData` element doesn't always contain information*
Like before, the syntax is slightly different than the previous two queries. 

![[event-viewer-xpath-query-eventdata.png]]

Using the information, we'll build a query for `TargetUserName`.
The query would be:
```powershell
Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"'
```



### Combining Multiple Queries
You can also combine queries using the keyword `and` as long as the entire query is wrapped in `''`

Example using the Above **Event ID** and **Provider Name** queries:
```powershell
Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=326 and */System/Provider[@Name="ESENT"]'
```

## Example  XPath Queries in Get-WinEvent
Example finding `WLMS` events with System Time `2020-12-15T01:09:08.940277500Z`
```powershell
Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"] and */System/TimeCreated[@SystemTime="2020-12-15T01:09:08.940277500Z"]'
```

Example find user name `Sam` with `Logon Event ID` of `4720`
```powershell
Get-WinEvent -LogName Security -FilterXPath '*/System/EventID=4720 and */EventData/Data[@Name="TargetUserName"]="sam"'
```

Example display PowerShell commands issued for event ID 4104 from exported Log File. 
```powershell
Get-WinEvent -Path 'C:\Users\Administrator\Desktop\merged.evtx' -FilterXPath '*/System/EventID=4104' -Oldest -MaxEvents 1 | Format-List
```
*Output*
[[get-winevent-xpath-query-event-id-powershell-cmd.png]]