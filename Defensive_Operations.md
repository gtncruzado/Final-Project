# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology
The following machines were identified on the network:
- Kali
  - **Operating System**: Debian Kali
  - **Purpose**: Pentester
  - **IP Address**: 192.168.1.90
- Target 1
  - **Operating System**: Debian Linux
  - **Purpose**: The WordPress Host
  - **IP Address**: 192.168.1.110
- ELK Stack
  - **Operating System**: Ubuntu 18.04
  - **Purpose**: The ELK (Elasticsearch and Kibana) Stack
  - **IP Address**: 192.168.1.100
- Capstone
  - **Operating System**: Ubuntu 18.04
  - **Purpose**: The Vulnerable Web Server
  - **IP Address**: 192.168.1.105



### Description of Targets

The target of this attack was: `Target 1`

Target 1 is an Apache web server with SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

![nmap display](/images/nmap%20(2).png)

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Error Code
Excessive HTTP Error Code is implemented as follows:
`WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes`
  - Metric: **WHEN count() GROUPED OVER top 5 ‘http.response.status_code’**
  - Threshold: **IS ABOVE 400**
  - Vulnerability Mitigated: **Enumeration/Brute Force**
  - Reliability: Highly reliable. Measuring by error codes 400 and above will filter out any normal or successful responses. 400+ codes are client and server errors which can be concerning. Especially when taking into account these error codes going off at a high rate.

![Excessive Error Code](/images/51%20Excessive%20HTTP%20Error%20Code.png)

#### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
`WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute`
  - Metric: **WHEN sum() of http.request.bytes OVER all documents**
  - Threshold: **IS ABOVE 3500**
  - Vulnerability Mitigated: **Code injection in HTTP requests (XSS and CRLF) or DDOS**
  - Reliability: Medium reliability. Possible false positives. May activate alert at large non malicious HTTP request or legitimate HTTP traffic.

![HTTP Request Size Monitor](/images/52%20HTTP%20Request%20Size%20Monitor.png)

#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:
`WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes`
  - Metric: **WHEN max() OF system.process.cpu.total.pct OVER all documents**
  - Threshold: **IS ABOVE 0.5**
  - Vulnerability Mitigated: **Malicious software, programs (malware or viruses) running taking up resources**
  - Reliability: Highly reliable. Even if there isn’t a malicious program running this can still help determine where to improve on CPU usage.

![CPU Usage Monitor](/images/53%20CPU%20Usage%20Monitor.png)
