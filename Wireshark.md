# Wireshark
One of the best packet analysis tools in industry, #wireshark has an immense community and plugins available to improve functionality.
Has a command line equivalent tool `tshark`#tshark

## Collection methods
### Network Taps
- physical implants which tap a cable #network_tap
- commonly used by #threat_hunting / #DFIR teams and #red_team in an engagement
	- **Vampire tap** which literally taps a cable and intercepts traffic across it
	- **Network Tap**, often called a star tap, which is planted between two network devices and replicates packets as they pass the tap

### MAC Floods
- #mac_flooding is a tactic commonly used by #red_team as a way of actively sniffing packets
- int
- ended to stress the switch and fill the #CAM tables, [content addressable memory tables](https://packet6.com/cam-table-fundamental-switch-operations/)
	- Once filled the switch will no longer accept new #MAC addresses and, in order to keep the network alive, will send out packets to all ports of the switch

#### ARP Poisoning
- #arp_poisoning is another #red_team tactic to actively sniff packets
- ARP poisoning redirects traffic from the host(s) to the  machine you're monitoring from
	- Does not stress network equipment like MAC flooding however should still be used with caution and only if other techniques like network taps are unavailable

### Promiscuous & Monitor Mode - Wireless
- #promiscuous_mode sniffs packets after connecting to an access point. Normally wireless packets are *marked* only for the intended recipient but promiscuous mode ignores that and captures all traffic regardless of recipient
	- Example: Having a conversation with a person (connection) while listening to all the conversations around you
- #monitor_mode sniffs packets without connecting to an access point.
	- Example: Walking down the street (connection-less) and listening to all the conversations as you pass by.
	- Lack of network connection means inability to process the Ethernet frame

## Filtering
Wireshark supports logical operators

Operation | Symbol(s)
------------ | ------------
and | and / &&
or | or / ||
equals | eq / ==
not equals | ne / !=
greater than | gt / >
less than | lt / <

Wireshark also has operators for *contains*, *matches*, and *bitwise_and*

Filtering can be accomplished with a combination of filters and operators
- Example:
	- `ip.addr == <IP ADDR>` returns all packets with an IP of IP ADDR
	- `ip.src == <SRC IP ADDR> and ip.dst == <DST IP ADDR` returns all packets with the specified source and destination IP ADDR
	- `tcp.port eq <PORT> or <PROTOCOL NAME>` returns all packets with a TCP port of PORT or with a packet of PROTOCOL NAME
		- e.g `tcp.port eq 60 or dcerpc`

## Structure
Packets consists of 5 of the 7 layers of the #OSI model
- Frame (Layer 1 - #Physical)
	- Shows what frame/packet you're inspecting and details specific to the Layer 1
-  Source \[MAC\] (Layer 2 - #Data_Link)
	-  source and destination #MAC addresses
-  Source \[IP\] (Layer 3 - #Network)
	-  source and destination IPv4 addresses
-  Protocol (Layer 4 - #Transport)
	-  details of the protocol used (UDP/TCP) along with the source and destination ports
-  Protocol Errors (cont. Layer 4 - Transport)
	-  specific segments from TCP that needed to be reassembled 
-  Application Protocol (Layer 7 - #Application)
	-  details specific to the protocol being used such as #http, #ftp, #smb, etc.
-  Application Data (cont. Layer 7 - Application)
	-  can show application specific data

## ARP Analysis
[[ARP]] - overview of the protocol

**ARP poisoning** is typically detected/recognized due to the high volume of ARP traffic from one specific source, even more so if that source is unrecognized. 

### ARP Request & Reply Packets
An ARP request packet is indicated by the value of the Opcode (operation code), where:
- *request* == 1
- *reply* == 2.

This is followed by the respective information for the operation, such as *Target Mac address* or *Sender MAC address* and *Sender IP address*

![ARP Request Packet|450](https://i.imgur.com/2NqTPz1.png)  ![ARP Response Packet|450](https://i.imgur.com/qnZyTVB.png)

To locate all request or reply packets in Wireshark, simply filter by the relevant information:
- Ex: filtering for reply:  `arp.opcode == reply` or `arp.opcode == 2`


## ICMP Analyis
[[ICMP]] - overview of #icmp protocol

### ICMP Traffic
ICMP can be used in some innovative ways but most commonly used for determining if a system is up. This means its filtered/dropped by firewalls to prevent threat actors from determining the existence of a system using just ICMP.

#### ICMP Request Packet

#### ICMP Reply Packet

