<- [[Malware Analysis]]
# Viper Monkey (vmonkey)
Github: 
- https://github.com/decalage2/ViperMonkey (orig/main repo)
- https://github.com/kirk-sayre-work/ViperMonkey (cutting edge update repo, sync'd *occasionally* w/ main)

Real Life Examples of Analysis with ViperMonkey: http://decalage.info/vba_emulation
Also demonstrated at Black Hat: see the [slides](https://decalage.info/en/bheu2019) and [video](https://youtu.be/l5sMPGjtKn0?list=PLH15HpR5qRsXiPOP3gxN6ultoj0rAR6Yn&t=1118) (at 18:38).


A #VBA (Visual Basic) emulation engine written in #Python, designed to analyze and deobfuscate malicious VBA Macros contained in #Microsoft Office files (Word, Excel, PowerPoint, Publisher, etc). 

## Usage
Basic usage can be accomplished by invoking the tool and passing a file (any applicable office format file):
`vmonkey <filename>.doc`

*Example output*
![[vmonkey-example-output.png]]