# Rapid7 compatible Custom Data Parsing Rules for Skyhigh Security Anomalies
This document contains a collection of Regular expressions that have been tested to work with Rapid7 insightIDM SIEM solution to parse fields form Skyhigh Security loglines that were transmitted to a Rapid7 event source for raw loglines via Syslog format directly.

These might change by version and are specific to the location of the field in the message. Rapid7 custom data parsing fetaure does not allow using regular expressions that would allow arbitrary locations of key-value fields as of April 2024

* LEEF formated Anomalies:

  - Alert.Data.DataTransfer
"source_data": "<14>Apr 18 18:49:04 W7LabPc LEEF:1.0|Skyhigh Security|Skyhigh Security Cloud|6.6.1.0|Anomaly|cat=Alert.Data.DataTransfer	devTimeFormat=MMM dd yyyy HH:mm:ss.SSS zzz	devTime=Apr 17 2024 22:19:15.000 UTC	usrName=c1eeefb535697e4434adc4c9edd7d4f8788f6cbe053a60d12ad0cbf100c01a07	dst=outlook.office365.com	sev=3	activityName=[Upload]	actorIdType=USER	classificationNames=[]	incidentId=SHW-163260311	riskSeverity=High	anomalyValue=199612833	thresholdValue=90040000	userAction=Upload	response=[Allowed]	serviceNames=[Microsoft Exchange Online]	status=NEW	updatedOn=Apr 17 2024 22:19:15.000 UTC"
--> Regex to parse the fields
^(?:.*Anomaly\|).*cat=(?P<cat>[^\t]+).*usrName=(?P<usrName>\w*).*dst=(?P<dst>[^\t]+).*sev=(?P<sev>[^\t]+).*activityName=(?P<activityName>[^\t]+).*actorIdType=(?P<actorIdType>[^\t]+).*classificationNames=(?P<classificationNames>[^\t]+).*incidentId=(?P<incidentId>[^\t]+).*riskSeverity=(?P<riskSeverity>[^\t]+).*anomalyValue=(?P<anomalyValue>[^\t]+).*thresholdValue=(?P<thresholdValue>[^\t]+).*userAction=(?P<userAction>[^\t]+).*response=(?P<response>[^\t]+).*serviceNames=(?P<serviceNames>[^\t]+).*status=(?P<status>[^\t]+).*
Tested with versions 6.6.1.0