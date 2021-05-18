<- [[Malware Analysis]]

# Static Malware Analysis
The process of analyzing malware without executing the code . 

## Entropy
TLDR: **Obfuscated/Encrypted malware will have high entropy**

File entropy can be very indicative of the suspiciousness of a file and is a prominent characteristic to evaluate. 

Simply, file entropy is a rating of how random the data within a file is. With a score from **0** to **8.0**, with **0** indicating less randomness and **8** indicating more random. 

For example, encrypted files will have a very high entropy score. 

Since malware authors commonly encrypt or pack their payloads to obfuscate their code and bypass anti-virus, these files will have **high** entropy. 

## Obfuscation
While there are legitimate reasons for obfuscation (such as protecting proprietary information), malware is frequently obfuscated to prevent or hinder analysis. 
- [[PE Explorer]]
- [[PeID]]

### Packing
One form of obfuscation is packing (see MITRE entry on [Software Packing](https://attack.mitre.org/techniques/T1027/002/), that is the compressing or encryption of an executable/code to conceal the code. Its legitimately used to reduce executable size and or protect intellectual property, but is also used by malware authors for the same reasons. 

#### How Packing Works
High-lvl, packers use an executable as a source and output's it to another executable that will have some modifications made depending on the packer. 

Executables have what's called an entry point, which is simply the location of the first pieces of code to be executed. 
![[how-packing-works-executable-entry-point.png]]

When an exectuable is packed it must unpack itself before any code can execute, so packers change the entry point from the original location to what's called the "Unpacking Stub"

![[how-packing-works-unpacking-stub.png]]

The "Unpacking Stub" will begin to unpack the executable into its original state. Once the program is fully unpacked, the entry point will now relocate back to its normal place to begin executing code. Its **only** at this point can analysts begin to understand what the executable is doing. Thankfully, this does mean that *memory dumps* during which an executable is running will have the unpacked state, since it must be unpacked to run. 
![[how-packing-works-unpacked-entry-point.png]]

#### Packed Files
Files that are packed have a few characteristics that may indicate whether or not they're packed:
- High entropy
- Few Imports (packed files may only have *GetProcAddress* and *LoadLibrary*)
- Executable may have sections named after certain packers (such as UPX)

*Imports of same executable: unpacked vs packed*

 | unpacked                                    | packed |
 | ------------------------------------------- | ------ |
 | ![[unpacked-executable-import-example.png]] | ![[packed-executable-import-list.png]]       |
 
## Tools
Notepad/Notepad++ or any basic text editor is always an indispensable tool

**Portable Executable**
- Dependency Walker
- [[PeID]]
- [[PE Explorer]]
- PEview
- ResourceHacker

**Disassembly/Hex**
- IDA Freeware
- WinDbg
- Radare2
- Ghidra
- HxD (Win)
- Strings
- 