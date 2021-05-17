<- [[ZeroLogon]]
# Zerologon PCAP Analysis
![[zerologon 1.pcap]]

#zerologon, or #CVE-2020-1472, is a Windows #Active_Directory Exploit. 
The pcap file from THM contains a #Windows #Domain_Controller with a private IP 191.168.100.6 and an attacker w/ private IP of 192.168.100.128.

## Identifying the Attacker

Immediately upon opening there's some normal #OpenVPN traffic and #SSDP ([[SSDP|Simple Service Discovery Protocol]]), #tcp, and #arp. But after that are two less common protocols, #DCERPC ([[DCERPC|Distributed Copmuting Environment/REmote Procedure Call]]and #EPM ([[EPM|Endpoint Mapper for DCERPC]], indicated by the red highlights.

![[Pasted image 20210429110209.png]]

More concerning is the presence of `RPC_NETLOGON` in the Info pane for EPM.

All the requests are coming from 192.168.100.128 so we can assume that they're the **attacker**. 

## POC Connection Analysis

Once the attacker is identified, we filter the PCAP for their IP as the SRC IP to isolate traffic originating from them. 
 `ip.src == 192.168.100.128`
 
Having prior knowledge of #IOC (Indicators of Compromise) for Zerologon, we would know that Zerologon is characterized by multiple #RPC connections and #DCERPC requests to change the machine account password. 

![[Pasted image 20210429112451.png]]

## Secretsdump SMB Analysis

Taking a peak at the Protocol Hierarchy for the pcap filtered by  `ip.src == 192.168.100.128`, we see the presence of #SMB traffic, as well as DCERPC and EPM. Essentially the attackers traffic is entirely suspicious
![[Pasted image 20210429113554.png]]

With prior knowledge of how the attack works, we know that Zerologon uses #secretsdump to dump hashes, which abuses #SMB2/3 and #DRSUAPI ([[DRSUAPI|Directory Replication Service via Microsoft API]], used in relation to #DC and #AD objects)

![[Pasted image 20210429114519.png]]

