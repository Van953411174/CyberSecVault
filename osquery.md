# osquery 
**Documentation**
- Daemon and CLI flags: https://osquery.readthedocs.io/en/latest/installation/cli-flags/
- Schema: https://osquery.io/schema/4.8.0/
-  File Integrity Monitoring: [https://osquery.readthedocs.io/en/latest/deployment/file-integrity-monitoring/](https://osquery.readthedocs.io/en/latest/deployment/file-integrity-monitoring/)
-   Process Auditing: [https://osquery.readthedocs.io/en/latest/deployment/process-auditing/](https://osquery.readthedocs.io/en/latest/deployment/process-auditing/)
-  Syslog Consumption: [https://osquery.readthedocs.io/en/latest/deployment/syslog/](https://osquery.readthedocs.io/en/latest/deployment/syslog/)
-  Community Projects: https://osquery.io/ (towards bottom of page)
 
 **What is it?**
Osquery is an #OSS tool created by #Facebook that can query one or multiple endpoints with [[osquery fleet managers]] using #SQL syntax. Can be installed on: #Windows, #Linux , #macOS, and #FreeBSD. Osquery supports [[osquery#File Integrity Monitoring|File Integrity Monitoring]], #continuous_monitoring, and [[osquery#YARA-based scanning|YARA]] scanning. 

Osquery logs can also be ingested by #Elastic and #Splunk. 

**Tools utilizing osquery**
- [[AlienVault OTX]] agent is based on Osquery. 
- Cisco AMP for endpoints utilize Osquery in [Cisco Orbital](https://orbital.amp.cisco.com/help/).

## Installation
If you wish to install Osquery on your local machine or local virtual machine, please refer to the installation instructions. 

-   [Install on Windows](https://osquery.readthedocs.io/en/stable/installation/install-windows/)
-   [Install on Linux](https://osquery.readthedocs.io/en/stable/installation/install-linux/)
-   [Install on macOS](https://osquery.readthedocs.io/en/stable/installation/install-macos/)
-   [Install on FreeBSD](https://osquery.readthedocs.io/en/stable/installation/install-freebsd/)


## File Integrity Monitoring
Osquery can be used for File Integrity Monitoring ( #FIM) in Linux (in `file_events`, using the inotify subsystem, and in `process_file_events` using the Audit subsystem), Windows (in `ntfs_journal_events`, using #NTFS Journaling) and macOS(in `file_events`, using FSEvents)

Collecting file events in osquery requires that you first specify a list of files/directories to monitor from the osquery configuration. The events that relate to those selected files will then populate the corresponding tables on each platform.

FIM is also disabled by default in osquery. To enable it, first ensure that events are enabled in osquery (`--disable_events=false`), then ensure that the desired FIM table is enabled with the corresponding CLI flag (`--enable_file_events=true` for `file_events`, `--disable_audit=false` for `process_file_events`, `--enable_ntfs_publisher=true` for `ntfs_journal_events`).

The three elements of a FIM config in osquery are (a) the scheduled query against `file_events`, (b) the added `file_paths` section, and (c) the `exclude_paths` sections.

The `file_events` query is scheduled to collect all of the FIM events that have occurred on any files within the paths specified within `file_paths` but excluding the paths specified within `exclude_paths` on a five minute interval. At a high level, this means events are buffered within osquery and sent to the configured _logger_ every five minutes. That is, the events are always recorded in real time; the interval is just how often the already recorded events will be logged.

To specify which files and directories you wish to monitor, you must use _fnmatch_\-style, or filesystem globbing, patterns to represent the target paths. You may use standard wildcards `*`/`**` or SQL-style wildcards `*%*`, see [[osquery#Matching Rules and Examples|Matching Rules and Examples]]

## Extensions
Anyone can create extensions for osquery (see https://osquery.readthedocs.io/en/latest/deployment/extensions/). 

Two repos with osquery extensions:
-   [https://github.com/trailofbits/osquery-extensions](https://github.com/trailofbits/osquery-extensions)
-   [https://github.com/polylogyx/osq-ext-bin](https://github.com/polylogyx/osq-ext-bin)
   
## YARA-based scanning
Complete Documentation:  https://osquery.readthedocs.io/en/stable/deployment/yara/

There are two [[YARA]]-related tables in osquery, which serve very different purposes. The first table, called `yara_events`, uses osquery's [Events framework](https://osquery.readthedocs.io/en/stable/development/pubsub-framework/) to monitor for filesystem changes and will execute #YARA when a file change event fires. The second table, just called `yara`, is an on-demand YARA scanning table.

## ATT&CK and Osquery
Mapping [[ATT&CK Framework]] to Osquery
https://github.com/teoseller/osquery-attck


## Using osquery
### Shell
Osquery has an interactive shell, run via `osqueri`, which is a modified #SQLite shell. 

All meta-commands are prefixed with `.`, such as `.help`. 

### Creating Queries
Queries uses a superset of SQLite language. Realistically all your queries will start with `SELECT` since you're only querying endpoints rather than updating/deleting information. 
- `UPDATE` and `DELETE` are valid but only if you're creating run-*time tabes (i.e. *views*) or using an extension that supports them. 

Some tables *will requires* a `WHERE` clause to return a value, such as the *file* table. Such tables will throw an error if `WHERE` is not in the query. 

#### SELECT, FROM
*Retrieve all information about running processes from endpoint*
```sql
SELECT * FROM processes;
```
- like any SQL-based query, data can be limited to just specified columns, such as  `SELECT pid, name, path FROM processes;`

#### WHERE
Use `WHERE` to narrow down results based on criteria

*Retrieve info on pid, name, and path where process name is `lsass.exe`*
```sql
SELECT pid, name, path FROM processes WHERE name='lsass.exe';
```

##### Filtering Operators for WHERE
Below are filtering operators that can be used in a `WHERE` clause:
-   `=` \[equal\]
-   `<> ` \[not equal\]
-   `>`, `>=` \[greater than, greater than or equal to\]
-   `<`, `<=` \[less than or less than or equal to\] 
-   `BETWEEN` \[between a range\]
-   `LIKE` \[pattern wildcard searches\]
-   `%` \[wildcard, multiple characters\]
-   `_` \[wildcard, one character\]

#### ORDER and LIMIT
Ordering output and limiting the amount of output is incredibly useful

*Order returned process information by name and limit to 3 entries*
```sql
SELECT pid, name, path FROM processes; ORDER by NAME LIMIT 3;
```

#### JOIN and USING
To join 2 or more tables each table needs to share a common column.  The schema for a table can be accessed with the `.schema` meta-command to compare columns.

*Join osquery_info and processes using the common column pid*
```sql
SELECT pid, name, path FROM osquery_info JOIN processes USING (pid);
```

![[osquery-join-with-pid-example.png]]

#### Matching Rules and Examples
**Wildcard Rules on Files/Directories**
-   `%`: Match all files and folders for one level.
-   `%%`: Match all files and folders recursively.
-   `%abc`: Match all within-level ending in "abc".
-   `abc%`: Match all within-level starting with "abc".

**Examples**
After you identify the files and directories you wish to monitor, add their match rules to the `file_paths` section in the osquery #FIM config.
-   `/Users/%/Library`: Monitor for changes to every user's Library folder, _but not the contents within_.
-   `/Users/%/Library/`: Monitor for changes to files _within_ each Library folder, but not the contents of their subdirectories.
-   `/Users/%/Library/%`: Same, changes to files within each Library folder.
-   `/Users/%/Library/%%`: Monitor changes recursively within each Library.
-   `/bin/%sh`: Monitor the `bin` directory for changes ending in `sh`.


**Note:** You cannot match recursively inside a path. For example `/Users/%%/Configuration.conf` is not a valid wildcard.
**Note:** Many applications may _replace_ a file instead of editing it in place. If you monitor the file directly, osquery will need to be restarted in order to monitor the replacement. You can avoid this by monitoring the containing _directory_ instead.
**Note:** Remember to specify home paths as, for instance, `/Users/%`, instead of `~/%` which will not work.

## Example Queries
Query for usernames from user table where username is 3 characters long and ends with 'en' (Ubuntu)
```sql
SELECT username FROM users WHERE username LIKE '_en'
```

The uid for *bravo* user (Ubuntu)
```sql
SELECT uid FROM users WHERE username='bravo'
```

With YARA on-demand scan all files within home directories for YARA sigfile match
```sql
SELECT * FROM yara WHERE pattern="/%%" AND sigfile="/var/osquery/yara/scanner.yar";

