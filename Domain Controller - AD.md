# Domain Controllers
A #DC is a Windows server that has Active Directory Domain Services ( #AD_DS) installed and has been promoted to #DC in the #forest. Domain controllers are the center of #AD , they control the rest of the domain. 
Tasks of a DC:
- holds the AD DS data store
- handles authentication and authorization services
- replicates updates from other DCs in the forest
- allows admin access to manage domain resources

## AD DS Data Store
Active Directory Data Store holds the databases and processes needed to store and manage directory information such as users, groups, and services.
Outline of some of the contents/characteristics of AD DS data store:
- contains the NTDS.dit 
	- database w/ all info of an AD DC as well as password hashes for domain users
- stored by default in %SystemRoot%\\NTDS
- accessible only by the DC


