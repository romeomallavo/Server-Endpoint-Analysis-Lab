# Server-Endpoint-Analysis-Lab
Analyzed WordPress logs, identified exploited plugins and malicious PHP web shell, and detailed attacker actions.

## Project Overview

This lab focuses on analyzing compromised WordPress site logs to identify potential vulnerabilities and trace malicious activities. Using Linux commands, we will extract and interpret data to understand what occurred on the server.

## Setup

1. **Create an Account on Blue Team Labs Online:**
   - Go to [Blue Team Labs Online](https://blueteamlabs.online).
   - Create an account if you haven't already.
   - Navigate to the "Challenges" tab and search for "WordPress."
   - Select the challenge: **Log Analysis D: Compromised WordPress**.

2. **Download and Extract the Log File:**
   - Download the `wordpress.zip` file provided in the challenge.
   - Extract it using the password `BT` (all lowercase):
     ```bash
     unzip wordpress.zip -P BT
     ```
   - Verify the extracted file:
     ```bash
     ls
     # Output: access.log
     ```

## Initial Analysis

1. **Check the First and Last Log Entries:**
   - Display the first 10 events:
     ```bash
     head access.log
     ```
   - Display the last 10 events:
     ```bash
     tail access.log
     ```

2. **Determine the Time Range of the Logs:**
   - The log entries cover data from January 12th, 2021, to January 14th, 2021.

3. **Extract and Count Source IP Addresses:**
   - Use the following command to extract and count unique source IPs:
     ```bash
     cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr
     ```

4. **Save IP Addresses to a File:**
   - Save the list of unique IPs to a file:
     ```bash
     cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr > ip.txt
     ```

## Analyzing User Agents

1. **Extract User Agents:**
   - Extract and sort user agents from the log file:
     ```bash
     cat access.log | cut -d'"' -f6 | sort | uniq -c | sort -nr
     ```

2. **Save User Agents to a File:**
   - Save the extracted user agents to a file:
     ```bash
     cat access.log | cut -d'"' -f6 | sort | uniq -c | sort -nr > user_agents.txt
     ```

## Investigating Suspicious Activity

1. **Identify POST Requests:**
   - Extract all POST requests from the log file:
     ```bash
     grep 'POST' access.log
     ```

2. **Exclude 403 Errors:**
   - Exclude any 403 (forbidden) errors from the POST requests:
     ```bash
     grep 'POST' access.log | grep -v '403'
     ```

3. **Save POST Request IPs to a File:**
   - Save the source IPs of POST requests to a file:
     ```bash
     grep 'POST' access.log | grep -v '403' | cut -d' ' -f1 | sort | uniq -c | sort -nr > post_ips.txt
     ```

## Investigating Plugins and Vulnerabilities

1. **Check Plugins Used:**
   - Identify and list plugins that may have been exploited:
     ```bash
     grep -i 'contact-form-7' access.log
     grep -i 'simple-file-list' access.log
     ```

2. **Research Vulnerabilities:**
   - Use Google to find CVE IDs and vulnerability details for the identified plugins, such as:
     - **Contact Form 7**: CVE-2020-35489
     - **Simple File List**: Exploit for version 4.2.2

## Final Analysis and Recap

1. **Summary of Findings:**
   - Both `contact-form-7` and `simple-file-list` plugins were activated on January 12th, 2021.
   - A malicious PHP file (`freak.php`) was uploaded via the `simple-file-list` plugin by an external IP.

2. **Key Findings:**
   - Two external IPs (from Taiwan and Japan) were observed interacting with the vulnerable plugins.
   - The attacker used tools such as `SQLmap` and `WPS scan` to identify vulnerabilities.

3. **Log Final Access and HTTP Response:**
   - The last access of the malicious PHP file returned a `404` response.

## Conclusion

This lab demonstrated how to analyze web server logs to identify and understand an attacker's activities. By identifying vulnerable plugins and tracking suspicious IP addresses and user agents, we were able to piece together the attacker's methodology.

## Resources

- [Blue Team Labs Online](https://blueteamlabs.online)
- Linux Commands: `grep`, `cut`, `uniq`, `sort`
