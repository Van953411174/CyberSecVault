<- [[MITRE]]
# CAR Knowledge Base
> The MITRE Cyber Analytics Repository (CAR) is a knowledge base of analytics developed by [MITRE](https://www.mitre.org) based on the [MITRE ATT&CK](https://attack.mitre.org/) adversary model. CAR defines a data model that is leveraged in its pseudocode representations, but also includes implementations directly targeted at specific tools (e.g., Splunk, EQL) in its analytics. With respect to coverage, CAR is focused on providing a set of validated and well-explained analytics, in particular with regards to their operating theory and rationale.

\- https://car.mitre.org/

Analytics stored in CAR contain the following information:
-   a _hypothesis_ which explains the idea behind the analytic
-   the _information domain_ or the primary domain the analytic is designed to operate within (e.g. host, network, process, external)
-   references to [ATT&CK](https://attack.mitre.org/) Techniques and Tactics that the analytic detects ( #ATTCK)
-   the [Glossary](https://car.mitre.org/Glossary)
-   a pseudocode description of how the analytic might be implemented
-   a unit test which can be run to trigger the analytic

## Use
The [Full Analytic List](https://car.mitre.org/analytics) contains all the models, and [ATT&CK Navigator layer](https://mitre-attack.github.io/attack-navigator/beta/enterprise/#layerURL=https%3A%2F%2Fraw.githubusercontent.com%2Fmitre-attack%2Fcar%2Fmaster%2Fdocs%2Fcar_attack%2Fcar_attack.json) captures the the current set of [[ATT&CK Framework]]tactics and techniques covered by #CAR (Navigator Layer shown below)
![[Pasted image 20210430123839.png]]

==The sections highlighted in **BLUE** are the analytics currently in CAR==
### Walk-through use w/ CAR-2020-09-001: Scheduled Task - FileAccess
Different analytics might have more sections, such as the presence of Unit Tests
Link to Analytic: **[CAR-2020-09-001: Scheduled Task - File Access](https://car.mitre.org/analytics/CAR-2020-09-001/)**.

#### Description
Opening presents user with brief description and explanation, as well as some information like Submission Date, Information Domain, Data Subtypes, Analytic Type, Applicable Platforms, and Contributors.
![[Pasted image 20210430124249.png]]

#### Detection and Model References
After the description is the ATT&CK Detection, with the mapped **Techniques**, **Subtechnique(s)**, **Tactics(s)**, **Level of Coverage**, followed by the Data Model References 

![[Pasted image 20210430124512.png]]
![[Pasted image 20210430124555.png]]


#### Implementations
Lastly, in the implementations section, we're provided pseudocode representation for the #Splunk search to find the analytic. After which follows an actual Splunk search and #LogPoint search that can be used to find the analytic. For other analytics the implementations available may vary, for example it might include an [#EQL](https://eql.readthedocs.io/en/latest/) implementation.
![[Pasted image 20210430125020.png]]
