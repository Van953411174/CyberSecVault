# Policies - AD
Big part of #AD and dictate how the server operates and what rules it will/will not follow. Domain policies are like domain groups except that instead of permissions they contain rules, and instead only applying to a group of users, policies apply to a domain as a whole. 

They simply act as rulebook for Active Directory that a domain admin can modify and alter as necessary to keep the network running smoothly + securely. 

Like default security groups, there is a **very** long list of default domain policies. Additionally, domain admins can choose to add in their own policies not already on the domain controller.
- Example: if you wanted to disable windows defender across all machines on the domain you could create a new group policy object to disable Windows Defender

The options for domain policies are almost endless and are a big factor for attackers when enumerating AD networks. 

Two examples of policies:
- Disable Windows Defender - Disables Windows Defender across all machines on the domain
- Digitally Sign Communication (Always) - Can disable or enable SMB signing on the #DC

### Group Policy Object - #GPO
Feature that adds additional controls to user accounts and computers

Group Policy settings include local settings, site-wide settings, domain-level settings and settings applied to organizational units

#### Creating a GPO
To create a GPO go to **Tools -> Group Policy Management** inside the Server Manager. 

Right click on **Group Policy Objects** and create a new object. 
Give it a name and adjust the object as needed.

For policies to apply, you need to link to the GPO to the root of the domain. 

