[[Windows - Event Logs]]
# Get-WinEvent
Docs: https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-5.1

A #PowerShell cmdlet that "gets events from event logs and event tracing log files on local and remote computers"
- *Get-WinEvent replaces Get-EventLog*

[Get-WinEvent cmdlet Cheat Sheet](https://wiki.sans.blue/Tools/pdfs/Get-WinEvent.pdf)

## Use
### General Filtering (but inefficient for large event logs)
```powershell
Get-WinEvent -LogName Application | Where-Object { $_.ProviderName - Match 'WLMS}'
```

When working with large event logs its inefficient to send objects down the pipeline to a `Where-Object` command. Instead use the *FilterHashtable* parameter.
#### Filter with Hashtables**
```powershell
Get-WinEvent -FilterHashtable @{ LogName = 'Application'; ProviderName='WLMS'}
```
Syntax of hash table is:
![[powershell-get_winevent-filterhashtable-syntax.png]]
*NOTE: You don't need a semicolon if each key/value pair is on a new line*
##### Hashtables
Guidelines for defining a hashtable:
- Begin the hash table with an at sign (@)
- Enclose the hash table in braces ({})
- Enter one or more key/value pairs for the context of the hash table
- Use an equal sign (=) to separate each key from its value

| Key name       | Value data type | Accepts wildcard characters? |
| -------------- | --------------- | ---------------------------- |
| LogName        | `<String[]>`    | Yes                          |
| ProviderName   | `<String[]>`    | Yes                          |
| Path           | `<String[]>`    | No                           |
| Keywords       | `<Long[]>`      | No                           |
| ID             | `<Int32[]>`     | No                           |
| Level          | `<Int32[]>`     | No                           |
| StartTime      | `<DateTime>`    | No                           |
| EndTime        | `<DateTime>`    | No                           |
| UserID         | `<SID>`         | No                           |
| Data           | `<String[]>`    | No                           |
| `<named-data>` | `<String[]>`    | No                           |

The `<named-data>` key represents a named event data field.

**NOTE**: some values might require specific entries to be valid, for example *Level* takes a Int32[], but unless you dig you wouldn't know that these don't correlate to the *Level ID* for Information (80) and others. See [here](http://msdn.microsoft.com/en-us/library/system.diagnostics.eventing.reader.standardeventlevel(v=vs.110).aspx) for the requisite values
#### Building Query
The information you need to build a query can be found in Event Viewer

![[event-viewer-hashtable-query-info.png]]

## Example Queries
See the docs for good starting point examples: [Get-WinEvent Examples](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-5.1#examples)

*Example of FIlterHashtable query*
```powershell
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-PowerShell/Operational'; ID=4104} | Select-Object -Property Message | Select-String -Pattern 'SecureString'
```

*Example FilterHashtable, find Process Name in events from archived log*
```powershell
Get-WinEvent -FilterHashtable @{ Path="C:\Users\Administrator\Desktop\merged.evtx"; Data="C:\Windows\System32\net1.exe" }
```