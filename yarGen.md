<- [[YARA]]
# yarGen
Another tool by Neo23x0 in #Python , used to generate [[YARA]] rules. 
Creates rules by cross referencing strings and #opcodes from goodware against #malware, thereby isolating characteristics of the malware that can be used to identify it. 
https://github.com/Neo23x0/yarGen

## Requirements
yarGen is **NOT** a lightweight tool. It cross compares the entirety of  file(s) with not insignificantly sized databases. As such its memory requirements are steep

Memory:
- Minimum - 4GB w/o opcodes
- Recommended - 8GB+ w/ opcodes

## Usage
Anytime before use, its a good idea to update #yarGen's databases. 
**NOTE** this does requires an active Internet connection.
To update default database: `python3 yarGen.py --update`


### First time 
Before first use, the goodware strings and opcodes are stored in ZIP archives that **must** be extracted. 

### Database Creation and Management
yarGen is only as good as its databases, and as such allows you to create new databases by analyzing goodware and also update existing databases. 

Updating default database can be accomplished with: `python3 yarGen.py --update`

Created databases will be loaded up with the default databases upon program execution.  

#### Creation
Supports creating multiple databases for opcodes and/or strings, and can easily be created with `-c` and set with a unique ID with `-i`:
Example:
- `yarGen.py -c --opcodes -i office -g /opt/packs/office365
	- `-c` create new database
	- `--opcodes` include opcodes
	- `-i` name it *office*
	- `-g` path to goodware
This will create the following new databases in the `./dbs` sub folder:
- `good-string-office.db`
- `good-opcodes-office.db`

#### Update
Similarly, you can update an existing database with the `-u` flag.
Ex: `yarGen.py -u --opcodes -i office -g /opt/packs/office365`

## Examples
See the Github for examples: https://github.com/Neo23x0/yarGen#examples

Example [video](https://medium.com/@cyb3rops/how-to-post-process-yara-rules-generated-by-yargen-121d29322282) on post-processing 
### Basic
Basic usage on suspicious or known malware file:
 - `python3 yarGen.py -m /home/cmnatic/suspicious-files/file2 --excludegood -o /home/cmnatic/suspicious-files/file2.yar`
	 - `-m` path to the files you want to generate rules for
		 - is recursive
	 - `--excludegood` force to exclude all goodware strings 
		 - *these are strings found in legitimate software and can increase false positive*
	 - `-o` location & name you want to output #YARA rule

After rule creation its good practice to examine the rule and remove any strings that you feel might generate false positives