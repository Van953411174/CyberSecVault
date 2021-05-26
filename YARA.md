# YARA 
Github: https://virustotal.github.io/yara/
Wiki: https://yara.readthedocs.io/en/stable/
What does it stand for? ... *Yet Another Ridiculous Acronym*
Absolute GOLD

For cheatsheet see here [[Cheatsheet-YARA]]

## What is it?
A pattern matching swiss-army knife for #malware researchers and everyone else. 
#YARA can identify information based on both **Binary** and textual patterns, such as hexadecimal and strings contained within a file. 

Rules are used to label these patterns, and are frequently written to determine if a file is malicious or not base upon features - or patterns - the file presents.

#malware ,like any program, uses strings to store textual data. For example, a #ransomware virus might store a #Bitcoin wallet address and a #botnet might store the IP address of the #C2 (command and control) server. 

The language that Yara uses **IS PROPRIETARY** which is worth keeping in mind. 

## Best Practice
[How to Write Simple but Sound Yara Rules - Part 1](https://www.bsk-consulting.de/2015/02/16/write-simple-sound-yara-rules/)

[How to Write Simple but Sound Yara Rules - Part 2](https://www.bsk-consulting.de/2015/10/17/how-to-write-simple-but-sound-yara-rules-part-2/)

[How to Write Simple but Sound Yara Rules - Part 3](https://www.bsk-consulting.de/2016/04/15/how-to-write-simple-but-sound-yara-rules-part-3/)

## Using YARA
Fairly trivial to pick-up, hard to master. Your rule is _only_ as effective as your understanding of the patterns you want to search for. 

Using a rule is simple though. Every `yara` command requires two arguments to be valid: ^94c118
1. the rule file we create
2. name of file, directory, or process ID to use the rule for. 

^requirements



**Every** rule must have a name and condition.
Example:
- `yara myrule.yar somedirectory`
	- .yar is the standard file ext. for all YARA rules. 

### Simple Example Rule Creation & Usage
Create a file called `myfirstrule.yar`:
![[Pasted image 20210501141037.png]]

Name of the rule is `examplerule` with one condition, in this case the condition is `condition`. This satisfies the two requirements [[#^requirements]]

This rule simply checks if the *file, directory, or PID* we specified exists via `condition: true`. If the file does exist, i.e. the patter has been met, we are given the output of `examplefule`+ `<providedArg>`. 
![[Pasted image 20210501142248.png]]

If the rule **fails** YARA will output an error. 
![[Pasted image 20210501142423.png]]

## Structure
YARA has many keywords and conditions allowing for as complex a rule as desired. Additional conditions have been added to the latest version v4.1.0 released April 26. 2021 (See official docs [here](https://yara.readthedocs.io/en/stable/writingrules.html))

The following is breakdown of some YARA rule components and their purpose:

### Meta & Desc
A `meta` section of a YARA rule reserved for descriptive information by the author of the rule, i.e. metadata. For example you can use `desc`, short for description to summarize what your rule checks for. Anything in this **does not** influence the rule itself, just useful for documenting. 
### Strings
You can use `strings` to search for specific text or hexadecimal in files or programs. `strings` takes string literals, #regex (enclosed in forward slashes not double quotes), as well as modifiers like `nocase` (ignore case) and `private` (never include match in output). 
Example strings rule:

	rule helloword_checker {
		strings:
			$hello_world = "Hello World!"
			$hello_world_lower = "hello world!"
			$hello_world_upper = "HELLO WORLD!"
			
		condition:
			any of them
	}

The condition `any of them` allows for multiple strings to be searched.
Alternatively, a modifier like `nocase` could had been appended to accomplish the same thing:

	rule helloword_checker {
		strings:
			$hello_world = "Hello World!" nocase
						
		condition:
			$hello_world
	}

#### Regular Expression
Regex are wrapped in forward slashes not double quotes like in most implementations. See the official documentation for recognized quantifiers, metacharacters, escapes, groupings, and character classes. 
Example regex usage:

	rule RegExpExample1
	{
		strings:
			$re1 = /md5: \[0-9a-fA-F\]{32}/
			$re2 = /state: (on|off)/
			
		condition:
			$re1 and $re2
	}

#### Modifier Table
<iframe src="https://yara.readthedocs.io/en/stable/writingrules.html#id3" class="resize-vertical"></iframe>


### Conditions
Conditions are nothing more than boolean expressions. They can contain the typical Boolean operators `and`, `or`, and `not`, and relational operators `>=`, `<=`, `<`, `>`, `==` and `!=`. Also, the arithmetic operators (`+`, `-`, `*`, `\`, `%`) and bitwise operators (`&`, `|`, `<<`, `>>`, `~`, `^`) can be used on numerical expressions.

Integers are ALWAYS 64-bits long, even the results of functions like *uint8*, *uint16*, and *unint32* are promoted to 64-bits. This must be taken into account when using bitwise operators (for example, `~0x01` is not `0xFE` but `0xFFFFFFFFFFFFFFFE`).

Operators do have a precedence, see [docs](https://yara.readthedocs.io/en/stable/writingrules.html#more-about-rules)

Example simple conditional operator usage, which matches any file or process containing string `$a` exactly `6` time, and more than `10` occurrences of string `$b`:

	rule CountExample
	{
		strings:
			$a = "dummy1"
			$b = "dummy2"

		condition:
			#a == 6 and #b > 10
	}

## Modules
YARA can be extended with modules, allowing for integration with other libraries like [[Cuckoo Sandbox]] or #Python_PE. Modules allow you to define data structures and functions which can be used in your rules to express more complex conditions. If you can't find a module for your needs, you can write your own (see [here](https://yara.readthedocs.io/en/stable/writingmodules.html#writing-modules) on how). 
Modules that come officially distributed with YARA are as follows:
- [PE](https://yara.readthedocs.io/en/stable/modules/pe.html)
    -   [Reference](https://yara.readthedocs.io/en/stable/modules/pe.html#reference)
-   [ELF](https://yara.readthedocs.io/en/stable/modules/elf.html)
    -   [Reference](https://yara.readthedocs.io/en/stable/modules/elf.html#reference)
-   [#Cuckoo](https://yara.readthedocs.io/en/stable/modules/cuckoo.html)
    -   [Reference](https://yara.readthedocs.io/en/stable/modules/cuckoo.html#reference)
-   [Magic](https://yara.readthedocs.io/en/stable/modules/magic.html)
-   [Hash](https://yara.readthedocs.io/en/stable/modules/hash.html)
-   [Math](https://yara.readthedocs.io/en/stable/modules/math.html)
-   [Dotnet](https://yara.readthedocs.io/en/stable/modules/dotnet.html)
    -   [Reference](https://yara.readthedocs.io/en/stable/modules/dotnet.html#reference)
-   [Time](https://yara.readthedocs.io/en/stable/modules/time.html)

## Tools and Pre-built Rules
There are a [plethora](https://github.com/InQuest/awesome-yara) of YARA tools and rules already built for use, including commercial products, meaning there are TONS of pre-built rules at your disposal. 

Some Tool examples, all written/designed by Neo23x0 (Florian Roth) follow.

### [[LOKI]]
[#LOKI](https://github.com/Neo23x0/Loki) is a simple, #OSS, #IOC and #IR Scanner implemented in #Python utilizing YARA.

### THOR
[#THOR](https://www.nextron-systems.com/thor-lite/) is an proprietary closed-source IOC and YARA scanner implemented #Go. The *Lite* version is free, though registration is required. Precompiled though binaries are platform specific. Features include:
- Scan throttling to limit CPU resource exhaustion

Its written in Go as corporate product, odds are its FAST.

### FENRIR
[#FENRIR](https://github.com/Neo23x0/Fenrir) is a simple #bash IOC checker using YARA. As such its very portable seeing as Windows now mostly supports bash. 

### [[yarGen]]
[#yarGen](https://github.com/Neo23x0/yarGen) is another tool by Neo23x0 in #Python . Just like the name implies, yarGen is a tool to generate YARA rules. 

### [[yarAnalyzer]]
[#yarAnalyzer](https://github.com/Neo23x0/yarAnalyzer/) is *another* tool by Neo23x0 in #Python. It creates statistics on a #YARA rule set and files. 

### [[Valhalla]]
[Valhalla](https://www.nextron-systems.com/valhalla/) is **_ANOTHER_** tool by Neo23x0 (jesus). Offered by his company Nextron Systems, Valhalla is a YARA rule feed with different categories of rules. Has a Web Frontend and API client. 
### YAYA
[#YAYA](https://github.com/EFForg/yaya) stands for *Yet Another YARA Automaton*, YAYA was buit by the #EFF and release 09/2020. 
> "_YAYA is a new open-source tool to help researchers manage multiple YARA rule repositories. YAYA starts by importing a set of high-quality YARA rules and then lets researchers add their own rules, disable specific rulesets, and run scans of files._"

\- [Introducing "YAYA", a New Threat Hunting Tool from EFF Threat Lab](https://www.eff.org/deeplinks/2020/09/introducing-yaya-new-threat-hunting-tool-eff-threat-lab)
As such its #OSS and #portable. 

	