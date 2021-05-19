# Active Directory
Active Directory ( #AD) is a directory service for Windows Domain Networks. It's used by many of today's top companies and is a vital skill to comprehend when attacking or defending Windows.  Below is information mainly pertaining to on-premise AD networks, see [[Azure Active Directory]] for cloud-based information.

## What is it?
Active Directory is a collection of machines and servers connected inside of domains, that are a collective part of a bigger forest of domains, that make up the Active Directory network. Active Directory contains many functioning bits and pieces. Below are various pieces of Active Directory: 
- Domain Controllers
- Forests, Trees, Domains
- Users + Groups 
- Trusts
- Policies 
- Domain Services

All of these parts of Active Directory come together to make a big network of machines and servers.
### Why use it?
It allows for control and monitoring of user's computers thorough a single #DC. It also allows a single user to sign in to any computer on the active directory network, assuming policy allows, and have access their stored files and folders in the server as well as local storage on that machine.

Allows for any user in the company to use any machine the company owns w/o having to set up multiple users on a machine.

## Structure and Components
As mentioned above, Active Directory has lots of bits and pieces that, together, form the system. 
### Physical 
Consisting of the servers and on-premise machines and can be anything from #DC's and storage servers; everything but the software.

#### Domain Controllers
![[Domain Controller - AD]]

### Forest
![[Forest - AD]]

### Trusts + Policies
![[Trusts - AD]]

![[Policies - AD]]


## Domain Services
The core functions of #AD network, allow for management of the domain, security certificates, LDAPs, and more. Is how the #DC decies what it wants to do and what services it wants to provide for the domain.

There are wide range of services that can be added to DC, but also default services.
Some Default Services:
- LDAP - #LDAP, or lightweight directory access protocol, provides communication between applications and directory services [[LDAP & LDAPS]]
- Certificate Services - allows the DC to create, validate, and revoke public key certificates
	- See X.500 series of certs https://en.wikipedia.org/wiki/X.500
- #DNS, #LLMR, #NBT-NS - Domain Name services for identifying IP hostnames

## Domain Authentication
Most important/vulnerable part of AD are the authentication protocols in place. 
Two main types of AD authentication:
- [[NTLM & NTLM 2]] - default Windows authentication protocol
- [[Kerberos]] - default authentication service for AD


## Users + Groups
By default a DC comes with default groups and two default users: *Administrator* and *guest*

### Users
Core to #AD, there are four main types of users, which more depending on how a company manages the permission of its users:
- Domain Admins - control the domain and are the only ones w/ access to the DC
- Service Accounts (Can be Domain Admins)
	- for the most part never used except for service maintenance
	- required by Windows for services such as SQL to pair a service with a service account
- Local Administrator - can make changes to local machines as an admin and may even be able to control other normal users, but _cannot_ access the DC
- Domain Users - everyday users that can log in to machines that they have the authorization to acess and may have local admin rights to machines depending on org
### Groups
Make it easier to give perms to users and objects by organizing them into groups w/ specified perms.
Two overarching type of AD groups:
- Security groups - used to specify permission for large number of users
- Distribution groups - used to specify email distribution lists
	- as an attacker these groups are less beneficial but can still be beneficial in enumeration

#### Default Security Groups
There are a LARGE number of default security groups. Here's a brief outline of some common ones:
-   Domain Controllers - All domain controllers in the domain
-   Domain Guests - All domain guests
-   Domain Users - All domain users
-   Domain Computers - All workstations and servers joined to the domain
-   Domain Admins - Designated administrators of the domain
-   Enterprise Admins - Designated administrators of the enterprise
-   Schema Admins - Designated administrators of the schema
-   DNS Admins - #DNS Administrators Group
-   DNS Update Proxy - DNS clients who are permitted to perform dynamic updates on behalf of some other clients (such as #DHCP servers).
-   Allowed RODC Password Replication Group - Members in this group can have their passwords replicated to all read-only domain controllers ( #RODC)  in the domain
-   Group Policy Creator Owners - Members in this group can modify group policy for the domain
-   Denied RODC Password Replication Group - Members in this group cannot have their passwords replicated to any read-only domain controllers ( #RODC) in the domain
-   Protected Users - Members of this group are afforded additional protections against authentication security threats. See http://go.microsoft.com/fwlink/?LinkId=298939 for more information.
-   Cert Publishers - Members of this group are permitted to publish certificates to the directory
-   Read-Only Domain Controllers - Members of this group are Read-Only Domain Controllers in the domain
-   Enterprise Read-Only Domain Controllers - Members of this group are Read-Only Domain Controllers in the enterprise
-   Key Admins - Members of this group can perform administrative actions on key objects within the domain.
-   Enterprise Key Admins - Members of this group can perform administrative actions on key objects within the forest.
-   Cloneable Domain Controllers - Members of this group that are domain controllers may be cloned.
-   #RAS and #IAS Servers - Servers in this group can access remote access properties of users

