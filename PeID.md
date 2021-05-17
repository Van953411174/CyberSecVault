<- [[Static Analysis]]
# PeID (or PEiD)
Nonofficial Wiki: [PEiD](https://www.aldeid.com/wiki/PEiD)

**What is it/does it do?**
- Detects most common packers, cryptors and compilers for PE files. 
- Currently detects more than 470 different signatures in PE files
- Checks file headers and trailers for hex signatures, e.g. `4D 5A` for executable

While it has a large database, its also not officially supported anymore which means its not necessarily up-to-date. Also, if a malware author has written their own, PeID will have no way of identifying the packer. 
In a lot of cases, its more about what PeID *doesn't tell us* rather than what it does. 

*Identifies packer as Microsoft Visual C++ 6.0 for file*
![[peid-static-analysis-example.png]]