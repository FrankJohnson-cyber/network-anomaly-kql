# Network Traffic Anomaly Detection with KQL

This project uses KQL to detect unusual outbound traffic patterns in Azure network logs, identifying high-volume sources and destinations.

## Query
```kql
AzureNetworkAnalytics_CL
| where TimeGenerated > ago(7d)
| summarize TotalBytes = sum(OutboundBytes_d), Destinations = make_set(DestIP_s), Protocols = make_set(L4Protocol_s), FirstSeen = min(TimeGenerated), LastSeen = max(TimeGenerated) by SrcIP_s
| where TotalBytes > 10000000 and array_length(Destinations) >= 10
| project SrcIP_s, TotalBytes, DestinationCount = array_length(Destinations), Destinations, Protocols, FirstSeen, LastSeen
| order by TotalBytes desc

#Results
Date: March 16, 2025
Findings: Identified 1 source IP (SrcIP_s) sending >10MB to 10 unique destination IPs in 7 days.
Sample Output:
SrcIP_s: "192.168.1.50" (example)
TotalBytes: 15,234,567
DestinationCount: 10
Destinations: ["203.0.113.1", "198.51.100.2", ...]
