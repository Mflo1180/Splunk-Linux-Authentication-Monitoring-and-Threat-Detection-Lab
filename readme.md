# Splunk Linux Authentication Monitoring and Threat Detection Lab

## Overview

This project focused on building a basic SIEM workflow using Splunk to monitor and investigate Linux authentication activity on an Ubuntu virtual machine.

The lab began with log ingestion and index creation, then progressed into detecting local authentication failures and externally generated SSH login attempts. The final outcome was a working Splunk monitoring pipeline capable of identifying repeated failed login behavior and highlighting patterns consistent with brute-force activity.

---

## Objectives

- Install and configure Splunk Enterprise on an Ubuntu virtual machine
- Ingest Linux authentication logs from `/var/log/auth.log`
- Store security events in a dedicated Splunk index
- Detect failed local privilege escalation attempts
- Simulate failed SSH login attempts from an external host
- Build Splunk searches to identify repeated failed login behavior and potential brute-force activity

---

## Lab Environment

- **Host system:** Windows PC
- **Virtualization platform:** Oracle VirtualBox
- **Guest system:** Ubuntu
- **SIEM platform:** Splunk Enterprise
- **Log source:** `/var/log/auth.log`
- **Custom index:** `security`
- **Source type:** `linux_auth`

---

## Project Workflow

### 1. Splunk Installation and Initial Configuration

Splunk Enterprise was installed locally on the Ubuntu VM.

During setup, I configured the platform to run properly in the lab environment and verified access through the local web interface. This established the core SIEM environment needed for the rest of the project.

---

### 2. Log Ingestion Setup

I configured Splunk to continuously monitor the Linux authentication log:

```text
/var/log/auth.log

This file contains security-relevant authentication activity such as:

SSH login attempts
Failed password attempts
su authentication failures
Session open and close activity
Privilege-related PAM events

I selected continuous monitoring rather than one-time ingestion so that new events would be searchable as soon as they were written to the log.

3. Custom Security Index Creation

Instead of using the default index, I created a dedicated index named:

security

This made the lab more realistic and organized by separating authentication events from unrelated data.

It also allowed more targeted searches such as:

index=security

This phase demonstrated how Splunk stores ingested log data in an organized, searchable structure rather than simply reading directly from the raw file each time.

4. Verification of Ingested Authentication Logs

After ingestion was configured, I verified that authentication events were appearing in Splunk.

This confirmed that:

Splunk was monitoring the correct file
The data source was active
The custom index was functioning properly
The linux_auth sourcetype was being applied

At this point, the lab had a working log pipeline from Ubuntu into Splunk.

5. Detection of Local Authentication Failures

The first detection use case focused on local failed privilege escalation attempts using su.

I intentionally generated failed su attempts inside the Ubuntu VM and searched for the resulting authentication failures in Splunk. This demonstrated that local suspicious activity could be identified through log analysis before moving on to network-based attack simulation.

This phase showed that the environment could detect:

Failed su activity
PAM authentication failures
Local privilege escalation attempts that did not succeed
6. SSH Service Enablement

To expand the lab into a more realistic external access scenario, I installed and enabled the OpenSSH server on the Ubuntu VM.

I then verified that the service was running and listening on port 22. This allowed the VM to accept remote SSH login attempts from the Windows host.

7. Network Configuration and Host-to-VM Connectivity

To simulate external login attempts, the VM network configuration was adjusted so that the Windows host could communicate with the Ubuntu VM directly.

I verified:

The VM’s active IP address
Connectivity between the host and VM
SSH service availability on the target system

This phase was important because it turned the VM into an actual network target rather than only a local lab box.

8. Simulated Failed SSH Login Attempts

From the Windows host, I used SSH to connect to the Ubuntu VM with an invalid username and intentionally entered the wrong password multiple times.

This generated repeated failed authentication events in /var/log/auth.log.

Example attack pattern:

Invalid user attempted over SSH
Repeated bad password submissions
Failure events logged by sshd
External source IP visible in the logs

This was the core attack simulation used for the remainder of the project.

Splunk Searches Used
Raw Failed SSH Detection

This search identified failed SSH login attempts:

index=security "Failed password"

This was the simplest proof that Splunk was correctly ingesting and surfacing failed SSH events.

Structured View of Failed SSH Events

This search displayed the key event metadata in a cleaner format:

index=security "Failed password"
| table _time host source sourcetype

This made it easier to verify when the events occurred, which host generated them, and which log file they came from.

Extracting Source IP and Counting Attempts

To identify the attacking source, I used the following search:

index=security "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort - count

What this search does:

Filters for failed SSH password events
Extracts the attacker IP from the raw event text
Counts failed attempts by source IP
Sorts the results from highest to lowest count

This search turned raw log entries into a basic brute-force detection view.

Pattern Over Time

To show the behavior over time rather than just total attempts, I used:

index=security "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bucket _time span=1m
| stats count by _time, src_ip
| sort _time

What this search does:

Extracts the attacker IP
Groups events into one-minute time windows
Counts attempts per time window per source IP
Shows the pattern in chronological order

This made it possible to demonstrate repeated failed attempts occurring in a short time span, which is more indicative of brute-force behavior than a single isolated failure.

Findings
Splunk successfully ingested Linux authentication logs from /var/log/auth.log
A dedicated security index was created and used for focused security searches
Local su failures were detectable through Splunk searches
External failed SSH login attempts were captured and searchable
The source IP of the attacking system was identifiable
Repeated failed SSH attempts from the same IP could be grouped and counted
Time-based grouping showed a repeated attack pattern over short intervals
Key Takeaways

This project demonstrated more than just installing Splunk.

It showed an end-to-end security monitoring workflow:

Collecting log data from a Linux system
Organizing data in a dedicated index
Validating ingestion and source configuration
Detecting both local and remote authentication failures
Extracting relevant details from raw logs
Converting repeated failed login activity into a simple detection use case

The most important part of the project was the progression from raw events to actual detection logic. Instead of stopping at “logs are visible,” the lab moved into identifying suspicious activity patterns and attributing them to a specific source IP.

Challenges Encountered
Splunk startup behavior after reboots required troubleshooting
Index visibility briefly caused confusion because of pagination and search context
Some searches required refinement because not all useful fields were automatically extracted
Network configuration had to be adjusted so that the Windows host could reach the Ubuntu VM over SSH

Working through those issues improved the realism of the project and reinforced troubleshooting skills in Linux, VirtualBox, networking, and Splunk search logic.

Conclusion

This project successfully built a basic Splunk-based authentication monitoring lab on Ubuntu and used it to detect suspicious login activity.

By ingesting auth.log, creating a dedicated security index, simulating both local and remote authentication failures, and building searches to identify repeated failed SSH attempts, the lab demonstrated a practical SIEM workflow that closely reflects entry-level security monitoring tasks.

Screenshot Section

Add screenshots for the following stages:

Splunk log source selection for /var/log/auth.log
Log monitoring configuration
Security index creation
Preview of authentication log events before ingestion
Successful ingestion of authentication logs
Failed local su / privilege escalation detection
SSH service running on Ubuntu VM
IP identification / network connectivity
Raw failed SSH events in Splunk
Structured failed SSH table view
Failed attempts grouped by source IP
Repeated failed attempts over time
Skills Demonstrated
Splunk installation and configuration
Linux log ingestion and monitoring
Custom index creation
Basic SPL query writing
Regex-based field extraction
Security event analysis
Linux authentication log interpretation
SSH service setup and testing
VirtualBox network troubleshooting
Brute-force detection logic
