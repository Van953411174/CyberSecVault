# SHIELD Active Defense
> Shield is an active defense knowledge base MITRE is developing to capture and organize what we are learning about active defense and adversary engagement. Derived from over 10 years of adversary engagement experience, it spans the range from high level, CISO ready considerations of opportunities and objectives, to practitioner friendly discussions of the TTPs available to defenders. 
> We are developing this knowledge base as both unstructured (think writings like blogs and papers) and structured (think tables of things, related to each other with links) data. Our first release (see more below) focuses on the structured elements. Working with the data can be awkward, so our presentation here is in the form of a couple of organizing views or “mappings”, including a mapping between MITRE ATT&CK® and Shield. We foresee additional views into the knowledge base we are developing.
> Shield is very much a work in progress; it is being released now not because it is complete, but because we think it is ready enough to stimulate conversations about active defense, adversary engagement, and the ways defenders can use them to change the game.

\- https://shield.mitre.org/

U.S. DoD defines active defense as “The employment of limited offensive action and counterattacks to deny a contested area or position to the enemy.”[^1]

[^1]:(https://www.jcs.mil/Portals/36/Documents/Doctrine/pubs/dictionary.pdf?ver=2020-06-18-073638-727)
 
In short MITRE's #SHIELD is a combination of light offense and countering capabilities like deception. **Its purpose is to enable the user to *trap* or *engage* the adversary active within a network. **

Example:
-	Planting decoy credentials on a resource and monitoring if/when the accounts credentials are used elsewhere within the network.
	-	By doing this we are alerted to the adversary's presence providing the opportunity to learn about their tools and tactics, classified as #threat_intelligence

## Use
Like [[ATT&CK Framework]], SHIELD has its own [matrix tool](https://shield.mitre.org/matrix/) consisting of 8 Tactics making up the columns, filled with Techniques describing how the defense achieves the Tactics. 

![[Pasted image 20210430133337.png]]

Like #ATTCK Clicking any Tactic (i.e. a column name) opens an information page of that Tactic, brief description and purpose, as well as a list of associated Techniques. The same occurs for clicking any specific Technique with relevant information being presented to the user. 
![[Pasted image 20210430134142.png]]

