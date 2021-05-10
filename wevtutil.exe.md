[[Windows - Event Logs]]
# wevtutil.exe
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

[[wevutil-security-queries.png]]