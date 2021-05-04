# ATT&CK Framework
>MITRE ATT&CK® is a globally-accessible knowledge base of adversary tactics and techniques based on real-world observations. The ATT&CK knowledge base is used as a foundation for the development of specific threat models and methodologies in the private sector, in government, and in the cybersecurity product and service community.

\- https://attack.mitre.org/

Utilizes #TTP (Tactics, Techniques, and Procedures) for mapping threats, incorporating known TTP for #APT groups. 
- Tactics - Adversary's goal or objective
	- e.g. theft, defacement, availability
- Technique - how the adversary achieves the goal or objective
- Procedures - how the technique is executed

## Cyber Threat Intelligence
#threat_intelligence ( #TI) or #cyber_threat_intelligence( #CTI) is the information, or #TTP's, attributed to the adversary. Using threat intelligence (see [[Cyber Threat Intelligence]]) defenders can make better decisions regarding defensive strategy. 

You can use ATT&CK for threat intelligence to determine and map APT groups who might target your company's sector and specific techniques used. Using this approach you can cross-reference your security posture with ATT&CK to check for gaps in coverage. 

### Breaking Down By Threat Group
Here is a link to the Threat Groups page, where each group is expanded upon: https://attack.mitre.org/groups/

When selecting a group, above the *Techniques Used* section is the option to Download or View the groups ATT&CK Navigation Layers similar to [[CAR Knowledge Base]]. 

![[Pasted image 20210430160420.png]]
![[Pasted image 20210430160451.png]]



## ATT&CK Matrix for Enterprise
There are 14 categories in the matrix, each containing techniques an adversary could use to perform the tactic. The categories cover the seven-stage [[Cyber Attack Lifecycle]] (based in part on Lockheed Martin's [[Cyber Kill Chain]])

Each Category has techniques listed inside, with some of those containing sub-techniques.  Example of **Initial Access** and *Phishing* sub techniques
![[Pasted image 20210430121436.png]]

Clicking on a Technique redirects to a page with all related information, brief description, *Procedure Examples* and *Mitigations*

## ATT&CK Navigator
MITRE built a simple tool for navigating the matrix, something people were/are already doing via tools like Excel.
Tool can be found here: https://mitre-attack.github.io/attack-navigator/
![[Pasted image 20210430122047.png]]






