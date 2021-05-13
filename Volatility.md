<- [[Memory Analysis]]
# Volatility


## Usage
### Determine Profile
`vol.py imageinfo -f filename` will provide list of profiles we can test on our file, however only one is correct. 
![[volatility-image-info.png]]

Validate profile selection with the following, the *more* valid profile will return a longer list of results:
`vol.py -f MEMORY_FILE.raw --profile=PROFILE pslist`
- `-f` file to analyze
- `--profile` profile to use for analysis
- `pslist` subcommand to print all running processes followed by EPROCESS lists

Example: `volatility -f cridex.vmem --profile=WinXPSP3x86 pslist`

### Processes
`pslist` - as used above, lists all running processes
`pstree` - print process list as tree
`psscan` - pool scanner for process objects

`psxview` -  find hidden processes with various process listings
`ldrmodules` - InLoad, InInit, InMem columns. If any of them are false its likely that module has been injected (use with grep)(follow up with *malfind*)

Example `ldrmodules`
![[volatility-ldrmodules-example.png]]

### Malicious Content
`malfind` - Find hidden and injected code, prints all flagged code to terminal 
Example: `volatility -f cridex.vmem --profile=WinXPSP3x86 malfind -D DESTINATION-DIR`
-	`-D`directory to save malicious process dumps and code

Example `malfind`
![[volatility-malfind-example.png]]

### DLLs  Loaded into Mem
`dlldump` - dump #DLL from a process address space, requires process pid
`dlllist` - print list of loaded DLLs for each process

Example `dlllist`
![[volatility-dlllist-example.png]]

Example `dlldump`:
`volatility -f cridex.vmem --profile=WinXPSP2x86 --pid=584 dlldump -D /tmp/
`
![[volatility-dlldump-example.png]]

### Network Connection
You can examine 