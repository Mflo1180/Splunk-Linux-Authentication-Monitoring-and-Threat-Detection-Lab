# Splunk Linux Authentication Monitoring and Threat Detection Lab

## Overview

This project demonstrates how to monitor and analyze Linux authentication logs using Splunk in a virtualized lab environment.

---

## Objectives

- Monitor `/var/log/auth.log`
- Detect failed login attempts
- Identify brute-force behavior

---

## Project Workflow

### 1. Splunk Installation

Splunk Enterprise was installed on the Ubuntu virtual machine and verified through the web interface.

---

### 2. Log Ingestion

Configured Splunk to monitor:

/var/log/auth.log

This file contains:
- SSH login attempts
- Failed passwords
- su authentication failures
- PAM events

---

### 3. Security Index

Created a dedicated index:

security

Example search:
index=security

---

### 4. Failed SSH Detection

Search used:
index=security "Failed password"

---

### 5. Source IP Extraction

Search used:
index=security "Failed password" | rex "from (?<src_ip>\\d+\\.\\d+\\.\\d+\\.\\d+)" | stats count by src_ip

---

### 6. Brute Force Detection

Search used:
index=security "Failed password" | rex "from (?<src_ip>\\d+\\.\\d+\\.\\d+\\.\\d+)" | bucket _time span=1m | stats count by _time, src_ip

---

## Key Takeaways

- Successfully ingested Linux authentication logs
- Detected failed SSH login attempts
- Identified repeated attack patterns
