# Azure Active Directory
See [[Active Directory]] for on-premise AD network

Active Directory can be cloud-based rather than on-premise. The most notable AD cloud provider is #Azure. Its default settings are much more secure than on-premise; however this is not to say AAD is more secure as there are different vulns and considerations that come with cloud services. 

Azure acts as the middle man between a physical AD and user's sign on. This allows for more secure transactions between domains, making a lot of "standard" AD attacks ineffective. 

![Basic AAD](https://i.imgur.com/J8q52i2.png)

## Windows Server AD vs. Azure AD
Brief comparison of on-premise vs Azure AD. [^1]

Windows Server AD | Azure AD
------------- | ------------
LDAP | REST APIs
NTLM | OAuth/SAML
Kerberos | OpenID
OU Tree | Flat Structure
Domains and Forests | Tenants
Trusts | Guests

[^1]:Tag Dump: #LDAP, #REST, #NTLM, #OAUTH , #SAML, #Kerberos, #OpenID_Connect, #OU, #forest, #tenants
