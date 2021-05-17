<- [[Malware Analysis]]
# peepdf.py - PDF Analysis Tool
Github: https://github.com/jesparza/peepdf

#Python tool to explore #PDF files in order to find out if the file can be harmful or not. 

Installation of [PyV8](https://github.com/buffer/pyv8) and [Pylibemu](https://github.com/buffer/pylibemu) allows #JS and #shellcode analysis also. 

*Taken directly from Github*
**Analysis:**
-   Decodings: hexadecimal, octal, name objects
-   More used filters
-   References in objects and where an object is referenced
-   Strings search (including streams)
-   Physical structure (offsets)
-   Logical tree structure
-   Metadata
-   Modifications between versions (changelog)
-   Compressed objects (object streams)
-   Analysis and modification of Javascript (PyV8): unescape, replace, join
-   Shellcode analysis (Libemu python wrapper, pylibemu)
-   Variables (set command)
-   Extraction of old versions of the document
-   Easy extraction of objects, Javascript code, shellcodes (>, >>, $>, $>>)
-   Checking hashes on **VirusTotal**

## Usage
Usage can be as simple as invoking the tool with a file as the argument. 
`peepdf <filename>.pdf`

The tool can be run as a **standalone scriptable** tool or with an **interactive console** using the `-i` flag. 

### Standalone
Using the tool in non-interactive mode requires passing commands/scripts with the `-s SCRIPTFILE / --load-script=SCRIPTFILE` flag. Otherwise the tool is rather limited. 

### Interactive
Passing the `-i / --interactive` flag initiates the interactive console mode. While in interactive mode, modules can simple be called by naming them and passing the requisite parameters. *FIRST* you must use the `open` command to load the file into the program, after which you can run any additional module commands on it.

*Interactive Mode*
![[peepdf-interactive-usage.png]]
## Extract Module
If you want extract embedded code without executing it, you can use the `extract` module! 

**To Use Standalone** - *extracting Javascript*
1. `echo 'extract js > <output-javascript>.pdf' > extract_javascript_script.txt`
	1. `<output-javascript>.pdf` file to write the extracted javascript to
	2. `extract_javscript_script.txt` the mini script to pass `peepdf`
2. `peepdf -s extract_javascript_script.txt`

**To Use Interactive** - *extracting Javascript*
1. `peepdf -i` open in interactive mode
2. `open <filename/path>` load the file
	1. *Example Interactive Open, red is JS and Suspicious* ![[peepdf-interactive-extract-example.png]]
3. `extract js` tell it to extract js from the now loaded file 
