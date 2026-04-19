# Splunk Linux Authentication Monitoring and Threat Detection Lab

## Overview

This project focused on building a basic SIEM workflow using Splunk to monitor and investigate Linux authentication activity on an Ubuntu virtual machine.

---

## Objectives

- Monitor `/var/log/auth.log`
- Detect failed logins
- Identify brute-force attempts

---

## Project Workflow

### 1. Splunk Installation

Splunk Enterprise was installed on the Ubuntu VM.

---

### 2. Log Ingestion

Monitoring file: `/var/log/auth.log`

This file includes:
- SSH login attempts
- Failed passwords
- su authentication failures
- PAM events

---

### 3. Security Index

Created index: `security`

Example search:

```spl
index=security
4. Failed SSH Detection
index=security "Failed password"
5. Extract Source IP
index=security "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
6. Brute Force Pattern
index=security "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bucket _time span=1m
| stats count by _time, src_ip
Key Takeaways
Successfully ingested Linux auth logs
Detected failed SSH attempts
Identified repeated attack patterns
