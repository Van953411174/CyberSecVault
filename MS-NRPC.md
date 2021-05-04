# Microsoft NetLogon Remote Protocol (MS-NRPC)
 
 [MS-NRPC](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/ff8f970f-3e37-40f7-bd4b-af7336e4792f)

Basic Netlogon Authentication Handshake, exploited in 2020 during [[ZeroLogon]] attack, specifically the *ComputeNetLogonCredential*
See [[ZeroLogon#Exploiting MS-NRPC]] for exploitation. 

The following information, with the exception of [[MS-NRPC#Additional Readings Sources]], is not up-to-date. It is in reference to the version exploited by ZeroLogon. 
## Authentication Handshake Broken Down

![[ms-nrpc-authentication-handshake.png]]



## ZeroLogon Vuln
See [[ZeroLogon]] for detailed information. 
See [[ms-nrpc-zerologon-attack.png]] for diagram of attack with referenced functions listed below in Additional Readings/Sources.


## Additional Readings/Sources
[3.1.4.1 Session-Key Negotiation](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/7b9e31d1-670e-4fc5-ad54-9ffff50755f9)
[3.5.4.4.2 NetrServerAuthenticate3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/3a9ed16f-8014-45ae-80af-c0ecb06e2db9)
[3.5.4.4.5 NetrServerPasswordSet2](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/14b020a8-0bcf-4af5-ab72-cc92bc6b1d81)
[NETLOGON_CREDENTIAL](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/d55e2632-7163-4f6c-b662-4b870e8cc1cd)

