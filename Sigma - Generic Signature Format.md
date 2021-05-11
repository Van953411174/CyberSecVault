<- [[SIEM]]
# Sigma
Github Repo: https://github.com/SigmaHQ/sigma
> "Sigma is for log files what Snort is for network traffic and YARA is for files" 



A generic and open signature format that allows you describe relevant log events in a straightforward manner. Created by Florian Roth, see [[YARA#Tools and Pre-built Rules]] for more things by him. 



**Requirements**
#Python >=3.5 and PyYAML
## Online Version
[Uncoder.io](https://uncoder.io/) - does more than just Sigma -> SIEM conversion, Also by Florian Roth

## Supported In/By
### Target SIEMS
-   [[Splunk]] (md link)
	-   [Splunk](https://www.splunk.com/) (plainqueries and dashboards)
-   [ElasticSearch Query Strings](https://www.elastic.co/)
-   [ElasticSearch Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)
-   [Kibana](https://www.elastic.co/de/products/kibana)
-   [Elastic X-Pack Watcher](https://www.elastic.co/guide/en/x-pack/current/xpack-alerting.html)
-   [Logpoint](https://www.logpoint.com)
-   [Microsoft Defender Advanced Threat Protection (MDATP)](https://www.microsoft.com/en-us/microsoft-365/windows/microsoft-defender-atp)
-   [Azure Sentinel / Azure Log Analytics](https://azure.microsoft.com/en-us/services/azure-sentinel/)
-   [Sumologic](https://www.sumologic.com/)
-   [[ArcSight]] (md link)
	-   [ArcSight](https://software.microfocus.com/en-us/products/siem-security-information-event-management/overview)
-   [QRadar](https://www.ibm.com/de-de/marketplace/ibm-qradar-siem)
-   [Qualys](https://www.qualys.com/apps/threat-protection/)
-   [[RSA NetWitness]] (md link)
	-   [RSA NetWitness](https://www.rsa.com/en-us/products/threat-detection-response)
-   [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/getting-started/getting-started-with-windows-powershell?view=powershell-6)
-   [Grep](https://www.gnu.org/software/grep/manual/grep.html) with Perl-compatible regular expression support
-   [LimaCharlie](https://limacharlie.io)
-   [ee-outliers](https://github.com/NVISO-BE/ee-outliers)
-   [Structured Threat Information Expression (STIX)](https://oasis-open.github.io/cti-documentation/stix/intro.html)
-   [LOGIQ](https://www.logiq.ai)
-   [uberAgent ESA](https://uberagent.com/)

### Projects/Products
-   [MISP](https://www.misp-project.org/index.html)
-   [THOR](https://www.nextron-systems.com/thor/) 
-   [Joe Sandbox](https://www.joesecurity.org/)