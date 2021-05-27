<- [[RangeForce - SOC Detection Challenges Index]]

# RF - YARA Rule Detection Challenge
[[YARA]]
> It is early 2017 and there is chatter on security forums about new malware spreading around like wildfire. You have gotten hold of some samples of the malware. Your goal is to analyze it and prepare yourself to be able to detect it in the future.

Index:
1. [[RF - YARA Rule Detection Challenge#Strings|Strings]]
2. [[RF - YARA Rule Detection Challenge#Simple Rule|Simple Rule]]
3. [[RF - YARA Rule Detection Challenge#Offset|Offset]]
4. [[RF - YARA Rule Detection Challenge#Windows Executables|Windows Executables]]
5. [[RF - YARA Rule Detection Challenge#Detect the Malware|Detect the Malware]]

## Strings
> The samples are located in the `/home/student/Desktop/sample` directory and aptly named `1`, `2` and `3`.
Perform **string analysis** on the files and try to look for rare strings that might be unique to the malware.


**How many sequences of printable characters with the minimum length of 7 are there in the file `sample/1`?**
*Assume printable characters to be single-7-bit-byte characters (ASCII, ISO 8859, etc.), including tab and space characters but no other whitespace characters.*

`1837`

- Process
	- To determine how many lines match navigate to the sample directory (or use the entire file path) and run: `strings ./1 | grep -E ".{7,}" | wc -l`

## Simple Rule
> During the analysis, you noticed that there was an interesting string `cmd.exe /c "%s"`. You want to detect this in the other sample files with a **Yara** rule. 

- Write a Yara rule to detect the presence of the string `cmd.exe /c "%s"`.
- Save the rule into the `/home/student/Desktop/rules/offset.yar` file.

I wrote a rule for regex matching of the string as I wasn't sure if the prompt was implying *any* string for the %s modifier or a literal %s modifier.

```
rule offset {
    strings:
        $c = /cmd\.exe \/c .{52}/
    condition:
        $c
}
```


## Offset
>What is the offset of the string `cmd.exe /c "%s"` in the file `sample/2`? Provide an answer in the hex form prepended by 0x e.g. `0x1234`.

`0x414d0`

- Process
	- Using the rule written in the previous task, simple give YARA the `-l` flag to return the line, and position, of matches in the file ![[RF-YARA-Rule-Detection-Challenge-Offset-of-match.png]]

## Windows Executables
> Your colleagues from the IT department have collected a number of suspicious files and placed a copy of them into the Desktop/suspicious directory.
You need to check how many of the files are Windows executables (PE file format), but there are too many files to analyze manually. You can leverage the power of Yara to simplify your work.

- Write a **Yara rule** capable of detecting files that have a **PE file format** in the `/home/student/Desktop/suspicious` directory.
- Save the rule into the `/home/student/Desktop/rules/pe.yar` file.

**Rule written for task:**
```
rule is_pe
{
	condition:
		uint16(0) == 0x5A4D and uint32(uint32(0x3C)) == 0x00004550
}
```
Taken from: https://countuponsecurity.com/2016/03/09/unleashing-yara-part-3/


## Detect the Malware
> You have gotten a list of strings commonly present in the malware from a threat intelligence community online. These strings are found in the file `Desktop/intel/strings.txt`.
Create a Yara rule out of these strings to detect the malware. You can also leverage the knowledge from previous sample analysis. Figure out how many files in the `Desktop/suspicious` directory are this malware.
Create the rule file into the `Desktop/rules` directory and name the file malware.yar.


- Write a **Yara rule** capable of detecting files that are actually malware in the `/home/student/Desktop/suspicious` directory.
	- Create the Yara rule from the strings in `/home/student/Desktop/intel/strings.txt` to detect the malware.
- Save the rule into the `/home/student/Desktop/rules/malware.yar` file.

**Rule Written for Task:**
The task was completed by parsing and cleaning the provided `strings.txt` file (300+ lines) using a python script to autogenerate the yara rule using anonymous strings. Making it non-anonymous is as simple as introducing a counter variable to prepend to each string. The rule's sensitivity can be adjusted by changing the `all of them` condition (requiring all strings to match) to `any of them` (requiring 1 or more to match) or to a specific number of them such as `5 of them`

*Script formatStr.py*
```python
startStr = '$ = "'
endStr = '"'
inputStrings = open("/home/student/Desktop/intel/strings.txt", 'r')
yaraRule = open("/home/student/Desktop/rules/malware.yar", 'w')


yaraRule.write("rule malware_detect{\nstrings:\n")

lines = inputStrings.readlines()
for l in lines:
    l = l.replace('\\', '\\\\')
    l = l.replace('"', '\\"')
    n = yaraRule.write(startStr+l[0:-1]+endStr+'\n')

yaraRule.write("\ncondition:\nall of them\n}\n")

inputStrings.close()
yaraRule.close()
```

After running the script `python formatStr.py` we're provided a YARA rule named `malware.yar` to use.

*YARA Rule*
![[RF-YARA-Rule-Detection-Challenge-yara-rule-malware-detect-head.png]]
![[RF-YARA-Rule-Detection-Challenge-yara-rule-malware-detect-tail.png]]