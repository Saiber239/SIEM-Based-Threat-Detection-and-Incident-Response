# SIEM-Based-Threat-Detection-and-Incident-Response
Overview

This guide provides structured exercises and advanced concepts to master Splunk as a Security Information and Event Management (SIEM) tool. It focuses on real-world cybersecurity incidents, enhancing skills in threat detection, investigation, and response.

Table of Contents

Splunk Enterprise Installation

Data Ingestion and Index Creation

Real-time Event Monitoring

Optimized Search Processing with SPL

Use Case: Brute Force Attack Detection

Advanced Threat Detection Techniques

Configuring Alerts for Threat Detection

Creating Dynamic Dashboards for Incident Response

Validation and Hands-On Exercises

Conclusion

1. Splunk Enterprise Installation

Follow these steps to install and configure Splunk for a SOC environment.

Step

Description

1

Download Splunk Enterprise from the official website.

2

Install Splunk and access it via http://localhost:8000.

3

Explore features like Search, Alerts, and Dashboards.

2. Data Ingestion and Index Creation

To simulate SOC data, security logs such as network traffic or syslog data are ingested.

Steps to Ingest Data:

Task

Action

Create Index

Navigate to Settings > Indexes > New Index and name it soc_lab.

Upload Data

Use BOTS v3 dataset or a syslog generator.

Verify Data

Execute:

index="soc_lab" | head 10

3. Real-time Event Monitoring

Live monitoring of security events is crucial in SOC environments.

Example: Detecting Failed Logins

index=soc_lab "failed login"
| stats count by user
| where count > 5

Filters help identify brute-force attacks in real time.

Figure 1: Example Failed Login Event Monitoring

4. Optimized Search Processing with SPL

Search Processing Language (SPL) enhances query efficiency.

SPL Best Practice

Example

Use Time Constraints

index=soc_lab earliest=-1h

Avoid Wildcards

index=soc_lab src_ip="192.168.1.*"

Event Sampling

`index=soc_lab

sample ratio=0.1`

5. Use Case: Brute Force Attack Detection

A common attack pattern involves repeated failed login attempts.

Search Query:

index=soc_lab sourcetype=access_combined_wcookie action=login
| stats count by src_ip
| where count > 100

Step

Action

Analyze

Identify IPs with 100+ failed attempts.

Remediate

Block or investigate the IP further.

6. Advanced Threat Detection Techniques

Insider Threat Detection

index=soc_lab action="login attempt"
| stats count by user status
| where status="failed" AND count > 3
| table user count

Identifies unusual login failures linked to insider threats.

Lateral Movement Detection

index=soc_lab dest_ip="10.0.0.*"
| stats count by src_ip, dest_ip
| where count > 50
| table src_ip dest_ip count

Detects unauthorized movement across internal systems.

Geolocation-based Threat Hunting

index=soc_lab | iplocation src_ip
| stats count by Country
| where Country="China"
| table src_ip Country count

Tracks login attempts from high-risk regions.

7. Configuring Alerts for Threat Detection

Steps to Configure a DoS Alert:

Run the Query:

index=soc_lab sourcetype=firewall action=deny
| stats count by src_ip
| where count > 100

Save the search as an alert.

Trigger alert when count > 100 within 5 minutes.

Figure 2: Example Alert Configuration

8. Creating Dynamic Dashboards for Incident Response

Dashboards enable visualization of threat data in real time.

Example Panel: Top 10 Blocked IPs

index=soc_lab sourcetype="firewall" action="deny"
| stats count by src_ip
| sort -count
| head 10
| table src_ip count

Adds interactive filters for better analysis.

Figure 3: Dashboard Example - Blocked IPs

9. Validation and Hands-On Exercises

Exercise 1: Alert for Non-US Outbound Traffic

index=soc_lab sourcetype=botsv3 
| iplocation src_ip 
| search Country!="United States" 
| stats count by src_ip, Country

Exercise 2: Failed Login Dashboard Panel

index=soc_lab sourcetype=access_combined_wcookie action=login
| stats count by src_ip
| sort -count
| head 10

Table 1: Validation Exercises Summary

Exercise

Description

1

Set an alert for non-US traffic using IP geolocation.

2

Create a dashboard for top 10 failed login source IPs.

Conclusion

This guide provides a step-by-step methodology to set up a SOC lab using Splunk as a SIEM tool. The lab incorporates security monitoring, optimized searches, advanced threat detection, and visualization techniques to improve cybersecurity and incident response capabilities.

