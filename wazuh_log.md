# Wazuh SIEM Homelab Setup

## Overview
Wazuh is a Security Information and Event Management (SIEM) platform. It collects logs, applies detection rules, and alerts on suspicious activity.

## Installation

Used the all-in-one installer:
```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

Access dashboard: `https://<server-ip>:443`

## Local Log Collection

Added `/var/log/auth.log` to Wazuh configuration:

```bash
sudo tee -a /var/ossec/etc/ossec.conf << 'EOF'
<localfile>
  <location>/var/log/auth.log</location>
  <log_format>syslog</log_format>
</localfile>
EOF
```

Restart manager: `sudo systemctl restart wazuh-manager`

## Custom SSH Detection Rules

Created rules to detect failed SSH login attempts:

```xml
<rule id="100001" level="5">
  <program_name>sshd</program_name>
  <match>Failed password</match>
  <description>SSH failed login attempt</description>
</rule>
```

**What it does:** Matches log entries containing "Failed password" from sshd and alerts with severity level 5.

## Alert Example
<img width="1887" height="893" alt="image" src="https://github.com/user-attachments/assets/796fafd3-91e4-42a2-b07b-b90602156767" />

Shows detection rules 100001/100002 firing on failed password attempts.

