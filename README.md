# Active Directory SOC Automation 

## Objective
The purpose of this project was to create a cloud-hosted Active Directory environment integrated with Splunk, Slack, and Shuffle SOAR to automate incident response for unauthorized successful logins.  
The environment simulates a real-world SOC workflow:
- Detect unauthorized RDP logins via Windows event telemetry sent to Splunk.
- Trigger an automated Slack alert.
- Email the SOC analyst for approval to disable the compromised account.
- Automatically disable the account in Active Directory upon analyst confirmation.

This hands-on project enhanced my technical skills in SOC tooling, automation, and Active Directory management while providing an end-to-end incident detection and response workflow.

## Skills Learned
- **Active Directory Administration** – Domain setup, user creation, and workstation domain joining.
- **SIEM Integration** – Configuring Splunk to ingest Windows event logs and building detection queries.
- **SOAR Automation** – Using Shuffle to create automated workflows for Slack notifications, email approvals, and Active Directory account actions.
- **Incident Response** – Automating the decision-making process for disabling compromised accounts.
- **Cloud Networking** – Configuring firewall rules, VPC networking, and secure remote access for virtual machines.
- **Windows Event Log Analysis** – Understanding Event ID 4624 (successful logins) and logon type codes for RDP detection.

---

## Tools Used
- **Vultr Cloud** – Hosting all virtual machines.
- **Windows Server 2022** – Domain Controller and Test Machine.
- **Ubuntu Server** – Hosting Splunk.
- **Splunk Enterprise** – SIEM for log collection, parsing, and alert creation.
- **Splunk Universal Forwarder** – Installed on endpoints to send telemetry.
- **Slack** – SOC notification channel.
- **Shuffle SOAR** – Automation workflows for detection and response.
- **Draw.io** – Creating the network/incident response architecture diagram.

---

## Steps

### Step 1 – Architecture Diagram
Created the full environment diagram using **Draw.io** to map virtual machines, attacker simulation, telemetry flow, and automation steps.  

![Architecture Diagram](images/architecture-diagram.png)

---

### Step 2 – Cloud Environment Setup
- Created **3 virtual machines** on Vultr:  
  - `my-dfir-ad-dc01` – Domain Controller (Windows Server 2022)  
  - Test Machine – Windows Server 2022 workstation joined to the domain  
  - `my-dfir-splunk` – Ubuntu server running Splunk
- Configured **VPC networking** and **firewall rules** for secure internal communication.

![Vultr VM Dashboard](images/vultr-vms.png)

---

### Step 3 – Active Directory Configuration
- Promoted `my-dfir-ad-dc01` to a domain controller (`mydfir.local`).
- Created test domain user: `Jenny Smith` (`jsmith`).
- Joined the Test Machine to the domain and enabled RDP access for `jsmith`.

![ADUC User Creation](images/aduc-jenny-smith.png)

---

### Step 4 – Splunk Installation & Configuration
- Installed **Splunk Enterprise** on Ubuntu (`my-dfir-splunk`).
- Installed **Splunk Universal Forwarder** on both the Domain Controller and Test Machine.
- Configured forwarding to index `my-dfir-ad`.
- Verified telemetry ingestion from both endpoints.

![Splunk Events from Endpoints](images/splunk-events.png)

---

### Step 5 – Detection Query & Alert Creation
- Built Splunk query for **Windows Event ID 4624** (successful logon) with RDP logon types (7 and 10).
- Filtered to detect **unauthorized** source IPs (outside "approved" IP range).
- Created scheduled Splunk alert:  
  `"My DFIR – Unauthorized Successful Login (RDP)"`

![Splunk Unauthorized Login Event](images/splunk-unauth-event.png)

---

### Step 6 – Shuffle SOAR Workflow
- Created Shuffle workflow triggered by Splunk Webhook.
- Steps in workflow:  
  1. Receive webhook from Splunk alert.  
  2. Send Slack notification to `#alerts` channel.  
  3. Email SOC analyst for approval to disable account.  
  4. If approved → Disable AD account via LDAP.  
  5. Post update in Slack confirming account action.

![Shuffle Workflow](images/shuffle-workflow.png)

---

### Step 7 – Slack Integration
- Created Slack workspace & `#alerts` channel.
- Configured Shuffle to send incident notifications to Slack with alert details (time, username, source IP).

![Slack Alert Notification](images/slack-alert.png)

---

### Step 8 – Testing the Workflow
- Simulated unauthorized RDP login to Test Machine using a VPN to generate foreign IP.
- Splunk detected event and triggered alert.
- Shuffle workflow ran, sending alert to Slack and email to analyst.
- Approved action via email → Account automatically disabled in AD.

![ADUC Disabled Account](images/aduc-disabled.png)

---

## Outcome
This project successfully demonstrated a fully functional SOC automation pipeline, from detection in Splunk to automated account disablement via Shuffle SOAR. It reinforced my understanding of integrating SIEM and SOAR platforms with directory services for rapid incident response.
