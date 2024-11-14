# SOC-Automation-Lab

The primary objective of this SOC Automation project was to develop an automated, scalable detection and response workflow for handling security events, focusing on seamless integration between the Wazuh SIEM, TheHive incident response platform, and Shuffle (SOAR platform). This project demonstrates my ability to set up a secure environment, manage firewall rules, configure monitoring tools, and automate alert responses using a Security Orchestration, Automation, and Response (SOAR) platform. This aimed to streamline threat detection, triage, and remediation for events like Mimikatz detections on Windows 11 hosted on a Virtual Machine running on an M1 Mac, empowering security analysts to respond effectively with minimal manual intervention. 

### Skills Learned
- **SOC Workflow Development**: Gained practical experience in structuring and deploying automated workflows for threat intelligence and incident response.
- **SIEM & SOAR Integration**: Learned how to configure Wazuh and Shuffle to work together, enhancing real-time alert processing and response actions.
- **Incident Response Automation**: Developed skills in automating common response tasks, such as IP blocking, alert escalation, and email notifications, which are crucial for managing high-volume security alerts in a SOC environment.
- **Scripting and Configuration Management**: Practiced editing configuration files, managing permissions, and using scripting to facilitate seamless tool interoperability, especially within Linux environments.
- **Regex & Hash Parsing**: Utilized regex techniques for parsing threat intelligence data, such as extracting hashes from alerts, and integrated VirusTotal for reputation checking.
- **User Interaction in Automated Workflows**: Implemented conditional decision-making within workflows, allowing analysts to interact with the system for specific responses, such as blocking IPs.


### Tools Used
- **Wazuh**: Employed as the central SIEM tool for log management, threat detection, and alert generation, configured to monitor and capture events like Mimikatz usage.
- **TheHive**: Used for incident tracking, case management, and threat analysis, enabling structured response to alerts generated by Wazuh.
- **Shuffle**: A SOAR platform used to automate response actions based on incoming alerts from Wazuh, allowing integration with external tools such as VirusTotal and email.
- **VirusTotal**: Utilized for hash reputation analysis, providing quick context on potential malware by evaluating SHA-256 hashes flagged in alerts.
- **DigitalOcean**: Hosted Wazuh and TheHive servers as well as network firewall configurations to manage traffic between security platforms.
- **Ubuntu & Windows 11 Virtual Machines on UTM**: Simulated real-world environments for generating telemetry data and testing automated workflows across different operating systems.
- **Regex**: Applied in Shuffle for parsing alert data, particularly useful for extracting critical information such as file hashes from Wazuh alerts.
- **Email Notification Services**: Enabled automated alerts to be sent to SOC analysts for high-priority events, ensuring timely incident response.

## Steps

### Step 1: Creating a SOC Automated Project Diagram
The first step in building a Security Operations Center (SOC) automation project is to create a clear visual diagram that outlines the flow of data and interaction between key components: Wazuh, TheHive, and Shuffle (Figure 1 & 2). This diagram will serve as a blueprint, focusing on the automation workflow and data paths to facilitate case management and alert response.
<img width="958" alt="step 1a" src="https://github.com/user-attachments/assets/24c474cf-edaa-4c72-bcab-12007f542143">
(Figure 1)

<img width="1029" alt="step 1b" src="https://github.com/user-attachments/assets/f0ca39b3-6252-44a7-b88a-6b1ac9601dfc">
(Figure 2)

#### Core Diagram Components

The emphasis should be on functionality rather than aesthetics. Each component represents a specific function within the SOC automation project:
- **PC Icon**: Represents a Windows 10 client with the Wazuh agent installed to simulate data flow from an endpoint.
- **Router Icon**: Depicts the network connection between the various components.
- **Cloud Icon**: Denotes internet resources and cloud-hosted services integrated within the project.
- **Windows 11 Client**: Endpoint device generating event data.
- **Internet (Cloud)**: Represents external internet and cloud services.
- **Wazuh Manager**: SIEM component for event collection and alert generation.
- **TheHive**: Centralised case management system.
- **Shuffle**: SOAR platform for orchestration and response.
- **SOC Analyst**: Person receiving and acting on alerts.

#### Establishing Workflow Steps

- **Step 1**: Windows 11 client sends events to Wazuh Manager.
- **Step 2**: Wazuh Manager collects events.
- **Step 3**: Wazuh Manager forwards alerts to Shuffle (color-coded arrow for distinction).
- **Step 4**: Shuffle enriches alerts with open-source intelligence for indicators of compromise (IOCs).
- **Step 5**: Enriched alerts are sent from Shuffle to TheHive for case management.
- **Step 6**: Shuffle emails the SOC Analyst about high-priority events.
- **Step 7**: The SOC Analyst receives the alert email.
- **Step 8**: The SOC Analyst initiates a response action as necessary.

- The SOC Analyst’s response is sent back to Shuffle, which forwards the action to Wazuh Manager for execution.
- Wazuh Manager then coordinates with the Windows 11 client to execute the required response action based on the SOC Analyst's input.

### Step 2: Project Setup

#### A. Virtual Machine (VM) Creation
- Created a Windows 11 virtual machine on Mac M1 to simulate a Windows environment for security monitoring. Due to the lack of compatibility coming from the M1 chip with Windows 11 ISO files, CrystalFetch and UTM are used as a VM host (Figure 3).
- installed Sysmon on Windows 11 virtual machine and ensured the service was active and running.
<img width="899" alt="step 2" src="https://github.com/user-attachments/assets/c34ee817-54a1-4960-8099-2adaee7b5d6a">
(Figure 3)

#### B. DigitalOcean Configuration
- Set up two cloud droplets on Digital Ocean for hosting Wazuh and The Hive instances (Figure 4)
- Configured firewall rules to control access (Figure 5):
  - Inbound Rules: Allowed TCP and UDP traffic only from my IP address (61.69.236.165) for secure and limited access.
  - Outbound Rules: No changes; kept the default settings to ensure general outbound connectivity.
<img width="1545" alt="step 2a" src="https://github.com/user-attachments/assets/4bf07ba6-af93-49f9-830e-6b59e2762947">
(Figure 4)
<img width="1529" alt="step 2b" src="https://github.com/user-attachments/assets/e5a34d65-8fd8-49c8-8f3e-076f38818ca5">
(Figure 5)

### Step 3: Configuring Security Monitoring Tools

#### A. Wazuh Installation and Setup

**Wazuh Installation**: Installed Wazuh 4.7 on one of the droplets.
- IPv4 Address obtained from DigitalOcean

**Firewall Integration**: Added firewall rules to allow Wazuh connections.

**Command Execution**: Accessed Wazuh via SSH, updated and upgraded the server, and installed the Wazuh package using:
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
  - After installation, store the login credentials for future use.
    
**Login and Configuration**: Logged into the Wazuh dashboard to configure detection rules for various threats (Figure 6).
<img width="1508" alt="step 3a" src="https://github.com/user-attachments/assets/5b84d361-4504-4e57-9f7c-925459b2fa15">
(Figure 6)
#### B. The Hive Installation and Setup

**The Hive Installation:**
- IPv4 Address: obtained from DigitalOcean
- Installed essential dependencies, Java (Corretto 11), Cassandra, and Elasticsearch, following best practices for security and performance optimization.
- Modified the cassandra.yaml file for The Hive, adjusting settings such as listen_address and rpc_address to match The Hive’s IP address.
  
**Configuration:**
- Updated The Hive configuration files for secure and efficient communication between services, including the privilege for The Hive to access certain files (Figure 7).
- Enabled and started services to ensure The Hive and associated tools were ready for integration. Ensure that all essential dependencies are active (Figure 8).
- Logged into The Hive dashboard to ensure that the connection is formed (Figure 9).
  
<img width="417" alt="step 3" src="https://github.com/user-attachments/assets/9bc9537d-5b97-4e8a-b178-d5d9a2aaebb1"> (Figure 7)
<img width="1033" alt="step 3b" src="https://github.com/user-attachments/assets/abc09881-b6f8-4bd4-a6a8-bfcff54912b4">
<img width="1502" alt="step 3c" src="https://github.com/user-attachments/assets/7e35324a-dd6b-4686-acfb-641cb59d66af">

### Step 4: Generating Telemetry and Configuring Alerts 

**Access Wazuh Configuration**
   - Go to `C:\Program Files (x86)\ossec-agent`.
   - Open the `ossec.conf` configuration file using Notepad (run as an administrator if necessary).
   - This file includes Wazuh's settings for log analysis and event handling.

**Modify Log Analysis Settings**
   - In the "Log Analysis" section, find event IDs marked for exclusion (indicated by `!=`).
   - Copy an existing line to customize settings for monitoring PowerShell and Mimikatz-related processes.

**Configure Sysmon Integration**
   - Created a backup (e.g., rename it `ossec_backup.conf`).
   - Reopened the file and add a specific tag for Sysmon logs, modifying an existing tag to reference Sysmon’s event channel from Windows Event Viewer.

**Streamline Log Forwarding**
   - Optionally, removed forwarding settings for less relevant logs (Application, Security, and System logs) if focusing on Sysmon logs only.
   - Saved the configuration changes.

**Restart Wazuh Service**
   - Went to Services and restart the Wazuh service to activate the new configurations.
   - Navigated to Wazuh dashboard under "Agent" to add new agent (Windows 11 Virtual Machine)


**Download and Prepare Mimikatz**
   - Download Mimikatz and adjust Windows Defender settings to exclude the Downloads folder (to prevent file interference).
   - If needed, adjust Google Chrome’s security settings to enable the download.
   - Extract Mimikatz for testing.

**Run Mimikatz**
   - Open an administrator PowerShell session, navigate to the Mimikatz directory, and execute `mimikatz.exe`.
<img width="717" alt="step 4b" src="https://github.com/user-attachments/assets/c905b315-6d1e-4846-a9f0-2cdf5ee0cbd4">

**Check for Mimikatz Events in Wazuh Dashboard**
   - Search for Mimikatz-related events in the Wazuh dashboard.
   - If no events appear, check that logging settings are properly configured, as Wazuh may only log events that trigger specific alerts by default.

**Update Wazuh Configuration for Comprehensive Logging**
    - Back up the `ossec.conf` file on the Wazuh manager.
    - Open `ossec.conf` and set `log_all` and `log_all_json` to "yes" to enable logging for all events.
    - Restart the Wazuh manager to apply these settings.

**Configure Filebeat**
    - Update the Filebeat configuration to allow ingestion of archived logs.
    - Restart the Filebeat service to implement changes.

**Set Up a New Index in Wazuh**
    - In the Wazuh dashboard, navigate to Stack Management, and create a new index pattern to monitor archived logs.

**Verify Log Ingestion**
    - Allow some time for log ingestion, then check the dashboard for Mimikatz events.
    - If events are not visible, troubleshoot by checking archive files directly.

**Create a Custom Alert Rule in Wazuh**
    - Use Wazuh’s built-in rules for reference.
    - Copy a Sysmon rule template (such as Event ID 1) and modify it to create a custom rule for detecting Mimikatz by its executable name.
    - Pay attention to proper syntax and case sensitivity when creating the rule.

**Test the Custom Alert**
    - Rename `mimikatz.exe` to an alternative name (e.g., "brianistryinghisbest.exe").
    - Run the renamed file, then check the Wazuh dashboard for any triggered alerts.
<img width="1633" alt="step 4c" src="https://github.com/user-attachments/assets/82288541-40c3-44a7-a44e-359ca1569b68">


### Step 5: Implementing with Shuffle
- The Shuffle general diagram would be as follows:
<img width="1069" alt="step 5 diagram" src="https://github.com/user-attachments/assets/ca30eb5c-a7f1-4c50-9f9f-93741ae4007f">

#### A. Shuffle Setup

**Create Account and Workflow:**
- Created an account on Shuffle and set up a new workflow named "SOC Automation Project".
- 
**Webhook Configuration:**
- Added a webhook trigger called "wazuh-alerts" to capture alerts from Wazuh, ensuring the webhook URI was correctly integrated into Wazuh’s configuration file.
- 
**Testing Webhook**: Generated sample alerts to verify successful event capture in Shuffle.
<img width="786" alt="step 5" src="https://github.com/user-attachments/assets/5858ac01-70dd-4d8d-84d7-61eda89551fa">

#### B. Wazuh Alert Configuration
**Configuration Modifications:**
- Added an integration tag for Shuffle in the Wazuh configuration file to streamline alert sending.
- Adjusted alert levels to use specific rule IDs for enhanced clarity.
**Telemetry Testing**: Ran Powershell commands to generate telemetry for testing webhook functionality.
<img width="1655" alt="step 5 Wazuh" src="https://github.com/user-attachments/assets/bd41f637-be50-4406-ad81-fe36b07b7fc2">

#### C. Parsing and Alert Processing

**Hash Parsing**: Used regex in Shuffle to extract SHA-256 hashes from alerts for further analysis.

**VirusTotal Integration:**
- Created a VirusTotal account and integrated it with Shuffle to check file hash reputations.
- Ensured correct API endpoints and resolved any connectivity issues.

#### D. Integrating with The Hive

**API Configuration:**
Established The Hive API connection using an API key to allow alert creation.

**Alert Attributes:**
Configured alert fields (severity, source, summary) to ensure detailed information for analyst review.
<img width="1517" alt="step 5 TheHive in Shuffle" src="https://github.com/user-attachments/assets/9faa2a05-1911-4a4a-ad35-17c0ba0867bb">

<img width="1545" alt="step 5 Thehive dashboard" src="https://github.com/user-attachments/assets/6a017556-5e36-4be1-97f5-d29835bdde6d">

<img width="1556" alt="step 5 theHive alert" src="https://github.com/user-attachments/assets/0bee86ee-140f-4997-bad0-657909dbd702">

#### E. Email Notifications

**Setup Email Notifications:**
- Configured Shuffle to send email alerts with incident details to analysts.
  
**Testing with Disposable Email:**
- Used SquareX disposable email addresses to test notifications without personal email risks.
<img width="779" alt="step 5 email" src="https://github.com/user-attachments/assets/fc61103e-1d9c-4977-b3c6-0d9f950c1011">

#### F. User Input for Blocking IPs (to be updated)

**User Decision Step:**
- Added a user input step in Shuffle to allow analysts to decide on IP blocking.
  
**Active Response Configuration:**
- Configured Wazuh’s active response to act on analyst decisions to block IPs.
<img width="931" alt="step 5 last" src="https://github.com/user-attachments/assets/8e7b09b1-1fbe-4139-a448-37d14513387b">

### Step 6: Testing and Further Directions
- Verified the entire workflow by generating alerts and confirming that each step—alert detection, data parsing, VirusTotal checks, The Hive alert creation, and email notifications—was functioning as expected.
- Further direction will try to implement SOC Automation on different OS such as Ubuntu machines or Linux. Further implementing different tools for more dynamic approaches such as Splunk.

## Conclusion
This project demonstrates my skills in cybersecurity monitoring, configuration management, automation, and incident response. By automating alert handling and response actions, I effectively reduced the manual workload for analysts and created a streamlined workflow for threat detection and mitigation. This SOC automation setup reflects a deep understanding of security operations and serves as a scalable, hands-on demonstration of my capabilities as a SOC analyst.
