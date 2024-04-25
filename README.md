# skyhigh-nxlog-rapid7
nxlog config to transfor Skyhigh SSE Incident, Anomalies and Audit Log to Rapid7 conform json events


Configure best-practice log flow for Rapid7 SIEM integration with Skyhigh Security

Source: <https://github.com/zengelan/skyhigh-nxlog-rapid7> 


## Abstract

This document describes how to set up the log flow between Skyhigh Security and Rapid7 InsightIDR SIEM system for best compatibility and functionality. Following these steps all Skyhigh Alerts, Events and audit logs will be available in Rapid7 InsightIDR in such way that all fields are parsed correctly and all filtering, slicing, grouping, aggregation and alerting functionality in Rapid7 can be used on each of the even fields.\
![](https://lh7-eu.googleusercontent.com/fcL7KsrI4Bu0RIMG4wJPPHaGs-nVqlCduN21IRlKfcNEovVc0wnJLehCYotcdZZRm9KAGKkWsTS3m2OkM0DavtHnubMkpArHVYliECl8BHBjxy5WnWVE9RZ72D8nlMGu4WCSqiUCw3ORoDjtODgKWrg)


## Motivation

There are several ways to ingest and parse custom or raw logs into Rapid7. The option Skyhigh suggests is to use NXlog for log transformation.

Rapid7 provides Regex based “Custom Parsing Rules” however due to the limitations of how this is implemented, this option is hard to use and to maintain. Rapid7 Regex doesn't allow “or” statements or multiple matches on a single line, so this means that the order of the key-value formatted fields always needs to be exactly the same. It is (as of April 2024) not possible to create regexes for custom parsing rules that could match the key-value pairs in arbitrary locations. This means that one would need to create custom parsing rules for each of different Skyhigh Alerts and Incidents which contain different fields and dynamic information. This option is therefore not the best option here.

Note: see Appendix 1 for an example on how to create a custom parsing rule to extract fields, with fixed order.

Rapid7 also allows vendors and customers to contribute EventSources as “Extensions” <https://extensions.rapid7.com/> Such extension is not yet (as of April 2024) available for Skyhigh Security and Skyhigh is getting in touch with Rapid7 to develop such native integration in the future. Skyhigh Security appreciates that customers could refer Rapid7 to Skyhigh to collaborate on creating such native integration. As this is not available today, this is also not an option.

Rapid7 also suggests using the tool NXlog for log transformation if neither a native event source is available nor if it’s possible to create custom parsers based on the regex functionality for a specific event source. <https://docs.rapid7.com/insightidr/nxlog/> This option is very versatile, scalable and error safe, hence this is the chosen option to recommend as a best practice to integrate Skyhigh with Rapid7 InsightIDR today (as of April 2024).

Rapid7 natively parses plain jason formatted string as loglines correctly with all available fields. So the strategy is to use nxlog between the Skyhigh Cloud Connect or and the Rapid7 collector to transform the loglines from Skyhigh SYSLOG LEEF Key-Value format to plain json formatted loglines.


## Architecture

![](https://lh7-eu.googleusercontent.com/lOGtXzcmJPKPOsrK7hHPoTWvisMsaRcoGsGQtFFyYH7BJXnmywwikgosGvuCpJxuJcrYmzcRR0uPr8eXLlb7zXUJ0Bct1BH9uW3FMmdxKbbFbldbGXzDuNqQ30nk2MP1ZGpuGhNQSAq3nNkLnlz9SQA)


# Setup procedure

1. ## Configure Rapid7 Event Source

- Login to Rapid7 and go to Data Collection Management.

- Click on Download Collector and install the collector on the server.![](https://lh7-eu.googleusercontent.com/I8ZCdV-nGDs5gA5ZyPR_EHx0EF5gU90OgzEWcq6hkIBWjigca-1QVffN9oqFuC5frWEUFNwdjZq6fI5RKr94ZkKBPjaLJycOceOsI7aD_7BWLudyNd2NGM5_XS4C80NXWkbP1fYtzKpLr06sn_giXIs)

- Then activate the collector as needed using the activation code.

* Now create a new event source that accepts incoming loglines from the nxlog service. Add a new event source , then chose “Add Raw Data” and select “Custom Logs”![](https://lh7-eu.googleusercontent.com/itfnvfHKLqy67YQnuDJ-3XrNv37RpYQNnn_vZHbRbj4wzNKLhR9Nq0kXtB53ATzmGxQDBpKTYemztuDt0qhTJv8xcVXQEmtFIuIGeb7atQ7wbk1Q6OV11snCSOS4EZJj8617H5VApWcW4Sp0ARwuYz0)

* Configure the Event Source to:

  - Name it specifically to Skyhigh. E.g. Skyhigh-nxlog

  - Select the collector that you just installed on the server

  - Select UTC timezone, or timezone configured on that server

  - Disable the option to “Parse RFC 3164 Syslog headers”

  - Enter port number 6678 (or another as desired, but needs to be changed in in nxlog config, too)

  - Select protocol UDP\
    ![](https://lh7-eu.googleusercontent.com/XH17yF5GPH2tiovgAr4OqwX29CZRYVIyloEgi22KtSwgvUBMeGn3ikesVCQieZV-xedN8zHJslNTrOyEYDOa31yp8HXGy6NtTnBB8zByxPYYYwXO3w6Lir__AOVZGDU1JbeqSG6fYy4ZoKBcsbsmHq8)

- Give it about 10 minutes, then ensure that the collector and event source are running.![](https://lh7-eu.googleusercontent.com/42lIiLj4xMx_mtjnJlHU8NaR_wbZ5vnMldPHPwql4oWsi07KZ6nkAmlBgoeOLaUxWbFvOSrGrPOJAqKjcEeQLjuarhY-u9b6oVcP2y1LKETEkgCOGeElZj7B1sVonRWsqMTU6QUVua3qGs3yvo9r6n8)

2. ## Setup nxLog Community Edition

nxLog Documentation available here: <https://docs.nxlog.co/ce/current/index.html#ref-config>&#x20;

- Install nxLog CE (Community Edition) from here <https://nxlog.co/downloads/nxlog-ce#nxlog-community-edition>

* Create a new file named `skyhigh_nxlog.conf` in the folder C:\Program Files\nxlog\conf\nxlog.d and paste the text from here: <https://github.com/zengelan/skyhigh-nxlog-rapid7/blob/main/skyhigh_nxlog.conf> 

- If desired, change the listening and forwarding ports in the file.

* Restart nxLog service by opening services.msc , select the nxlog service and click restart\
  ![](https://lh7-eu.googleusercontent.com/r53uEujEvxVhiYursM8ZBwMxPxuhAnXW98vQx8N6PNsm6weMv1V9LsO_f_O3bMu3QmLxgSZSRPzuUgtJwRcSRc412UHbi1tiB-_6hGXcf-Wp8sIRLpvsBT0bRiovA28horpVAzEqnTdcB3C9qeDdYOI)

- Then open and check the nxlog.log file in the folder C:\Program Files\nxlog\data we would expect to see the last line to show that nxlog was started. E.g. `INFO nxlog-ce-3.2.2329 started`![](https://lh7-eu.googleusercontent.com/3NZRCJdakvRKh00r21_q6YJ37d_L-n3l-Imo2XbQdAueihtDUnTgFR2uAU48ZhbHAKrHElGjfuwpj5GZQJzCvXrlFNXLIYbZ4m8Z7YMzEwxmo-b4X5rrzLeS1hKy6_nigLjeYYE2XDZLf5YULG3CWOs)

Nxlog is now ready to receive, parse, reformat and forward the loglines from Skyhigh Cloud Connector on TCP port 1514 to Rapid7 Collector Event Source listening on port UDP 6678.

Nxlog will receive SYSLOG formatted, LEEF based key-value loglines from Skyhigh Cloud Connector and will transform them into raw json formatted loglines which are sent to Rapid7.

3. ## Setup Skyhigh Cloud Connector

Skyhigh CLoud COnnector documentation can be found here: <https://success.skyhighsecurity.com/Skyhigh_CASB/Skyhigh_Cloud_Connector> 

- Download Skyhigh Cloud Connector from <https://success.skyhighsecurity.com/Software_Downloads/Download_Software/Download_Skyhigh_Cloud_Connector>

* Install the cloud connector and connect it to the Skyhigh tenant as per documentation

* Once the Cloud Connector is installed, login to Skyhigh Management console at <https://auth.ui.trellix.com/> and login as tenant administrator

* Go to Settings - Infrastructure - Cloud Connector\
  ![](https://lh7-eu.googleusercontent.com/aI0SQ4BVdkUCUbSk5VObaR-7weJ4jJXBD5iRz16Cmr128_AMxgdTDhtZsxDBwAtPszkOmrCxNZ4CB-xT-69rQhceBsxnWXs6qMyCVd6w0B5kFyw8OEu3BXuFDh4IooR38sGlMAlN-oTaXh57362xAw4)

* Select the right connector from the drop down list on top of the page (you can get the name of the connector by looking into the file named `logprocessor.install.properties` in the install directory on the server you installed it)

* Go to SIEM Integration (SaaS) and configure the settings as:

  - SIEM Server Host: localhost

  - SIEM Server Port: 6679    ( or the port nxlog is configured to listen on)

  - SIEM Protocol: TCP

  - Data Export Format Type: Log Event Extended Format (LEEF)

  - Anomaly Export Type: New Anomalies Only

  - Incident Export Type: New Incidents Only

  - Audit Log Export Type: New Audit Logs Only

![](https://lh7-eu.googleusercontent.com/Ji4S3ArH8r_3ube2Szxm01GDhkrH1uzrAz4wZc6cmWHsWySjuQDahmlR0lW29skrPqlAed7W3Zl481jYvmsMI6podhZ_NDK-Tdm-I5JLBcPOO-afKsyGelWHaexY6w3bDaedoFp0dF1_FavOfKNXisI)

- Click Save

- Wait about 30 minutes to get the settings in effect

Note: you can also configure the Export Types to “All Audit Logs”, “All Anomalies”, “All Incidents” to get a full set of all historic loglines imported in bulk. It is suggested to test first with a few loglines before enabling this type of bulk import as this could import hundreds of thousands loglines

All changes to this screen for Skyhigh Cloud Connector can take 30 minutes or even hours to go into effect.

