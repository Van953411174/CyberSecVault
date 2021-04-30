# LDAP & LDAPS
The main difference between #LDAP and #LDAPS is that LDAPS support encryption and therefore the credentials are not sent in plain text across the network.

Another thing to keep in mind is that the Domain Controller ( #DC) can be considered a database of users, groups, computers and so on (contains information about objects). Using LDAP/LDAPS the user's workstation sends the credentials using an API to the Domain Controller in order to validate them and be able to log in.
![LDAP](https://i.imgur.com/Vep5s0C.png)