# Windows Authentication

## Local Authentication
Local Authentication is done using the Local Security Authority #LSA . LSA is a protected subsystem that keeps track of the security policies and accounts that are on a computer system. Also maintains information about all aspects of local security on a computer.

## Active Directory
[[Active Directory]]
Two Types of #AD:
- On-Premise Active Directory ( #AD)
- Azure Active Directory  ( #AAD)

### Auth on On-Premise Active Directory
On-premise AD has a record of all users, PCs, and Servers and authenticates the users signing in (the network logon). 
Once signed in, AD also governs what the users are, and are not, allowed to do or access (authorization).

In an On-premise AD environment the authentication can be made using the following protocols:
- [[NTLM & NTLM 2| NTLM/NTML2 ( NT (New Technology) LAN Manager)]]
	- #NTLM and #NTLM2
- [[LDAP & LDAPS| LDAP/LDAPS (Lightweight Directory Access Protocol/over SSL]]
	- #LDAP and #LDAPS
- [[Kerberos]]
	- #Kerberos 


### Auth on Azure Active Directory
#Azure Active Directory ( #AAD) is a secure online authentication store, which can contain users and groups. Users have a username and a password which are used when you sign in to an application that uses Azure Active Directory for authentication. So, for example, all of the Microsoft Cloud services use Azure Active Directory for authentication: Office 365, Dynamics 365 and Azure.

Azure Active Directory supports the following authentication methods:

-   [[SAML]] (Security Assertion Markup Language)
	-   #SAML #SSO using markup
-   [[OAUTH 2.0]]
	-   #OAUTH 
-   [[OpenID Connect]]
	-   #OpenID_Connect ( #OIDC) is built on top of #OAUTH 2.0
	-   #JSON Web Tokens ( #JWT)
-  