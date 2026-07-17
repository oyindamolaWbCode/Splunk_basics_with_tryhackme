Splunk VPN Log Analysis

Project Overview
This repository showcases the ingestion, parsing, and targeted analysis of VPN access logs utilizing Splunk Enterprise. The project demonstrates how to track user activity, monitor source IP addresses, filter by geographic locations, and audit connection states using Splunk Search Processing Language (SPL).

Data Ingestion
The first step in the workflow involves onboarding the raw VPN log data into the Splunk environment.

Data Format: JSON

Target Index: vpn_log

Source Type: _json

Process: The raw log file (VPN-logs-1663593355154.json) was uploaded directly to the Splunk search head. You can view the final review screen of this data ingestion process in the file named vpn-log-upload.png.

Log Structure & Extracted Fields
Because the data was ingested as a JSON payload, Splunk automatically parses and extracts the key-value pairs at search time. By querying the base index, we can see the structure of the data payloads. Please refer to field.png for a look at the raw events.

Key interesting fields extracted include:

Company

EventTime

Source_Country

Source_ip

UserName

action (e.g., teardown, built)

port

protocol

source_state

Threat Hunting & Analysis Queries
1. Baseline Event Count
To understand the scope of the ingested dataset, a baseline statistical count was executed against the index.

SPL Query: index="vpn_log" | stats count

Result: 2,862 total events were successfully indexed.

Reference File: stats-count.png

2. User-Specific Activity Auditing
Tracking individual user access is a core component of VPN auditing. We can filter the data to look at connections established by specific employees.

SPL Query: index="vpn_log" | spath | search UserName="Maleena"

Result: Identified exactly 60 connection events associated with the user "Maleena".

Reference File: maleena-related.png

3. Geographic Filtering
To analyze traffic patterns based on region, or to hunt for anomalous logins outside of expected operating areas, we can filter by the Source_Country field. In this example, we excluded traffic from France to measure the rest of the global traffic.

SPL Query: index="vpn_log" | search Source_Country!="France" | stats count

Result: 2,814 events originated from countries other than France.

Reference File: other countries.png

4. IP Address Association
Correlating activities to a specific IP address is vital for incident response and tracking potentially compromised accounts. We ran two queries targeting the IP 107.3.206.58.

Raw Event Inspection: Running index="vpn_log" | search Source_ip="107.3.206.58" exposes the raw logs, showing that this IP is associated with the user "Will Smith" connecting from Virginia. Refer to the file named associate-vpn.png to view these logs.

Aggregated Count: Running index="vpn_log" | search Source_ip="107.3.206.58" | stats count reveals that there are exactly 14 events tied to this specific IP address. Refer to the file named associated-vpn.png for this output.
