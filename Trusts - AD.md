# Trusts - Active Directory
A mechanism in place for users in the network to gain access to other resources in the domain. For the most part, trusts outline the way that the domains inside of a #forest communicate to each other, in some environments trusts can be extended out to external domains and even forests in some cases.

Two types: 
- Directional - direction of the trust flows from a **trusting**domain to a **trusted** domain
- Transitive - trust relationship expands beyond just two domain to include other trusted domains
	- i.e. A trusts B trusts C, so A trusts C ==CHECK==