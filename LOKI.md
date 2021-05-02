# LOKI
Github repo: https://github.com/Neo23x0/Loki
#IOC and #IR scanner written in #Python utilizing [[YARA]] 

#LOKI comes with a set of #YARA rules alre
## Detection
Detection is based on 4 methods, though additional checks can be configured:
1.  File Name IOC Check
2.  YARA Rule Check
3.  Hash Check
4.  #C2 Back Connect Check

Additional Checks
1. Regin filesystem check (via --reginfs)
2. Process anomaly check (based on [Sysforensics](http://goo.gl/P99QZQ)
3. SWF decompressed scan (new since version v0.8)
4. SAM dump check

## Usage
Running `python loki.py -h` will display what options are available. 
Before running the tool for the first time, run with the `--upgrade` option.
**FIRST:** This adds the `signature-base` directory which LOKI uses to scan for known evil.

Example run:
`python loki.py -p /path/to/suspicious/fileOrDir`




## Included Rules
The `yara` directory in `signature-base` holds all the YARA rules that LOKI uses to hunt. Likewise the `iocs` folder holds, you guessed it, IOCs. 

