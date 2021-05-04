# yarAnalyzer
A [[YARA]] tool for analyzing #YARA rule set and files. 

## Usage
Very simple to use, first place rule set you want analyzed in the `/signatures` directory
Two methods of use,
### First method
generates file statistics and rule statistics. Run tool on desired file sample
`yarAnalyzer.py -p /sample/path -s /signatures`

Generates two tables as command line output and two CSV files:
*yaranalyzer_file_stats.csv, yaranalyzer_rule_stats.csv*

### Second method
Inventory creation, that is it creates a CSV default name `yara_rule_inventory.csv` (can be set with `-o`) with information about the initialized rules.
Rule File; Rule Name; Description, Reference

`yarAnalyzer.py --inventory -s /signatures`

