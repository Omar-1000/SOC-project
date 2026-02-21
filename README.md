# SOC-project
main idea : SOC mini-lab using Splunk to collect and correlate Windows Sysmon logs across two attack scenarios mapped to the Cyber Kill Chain. Includes setup steps, SPL queries, dashboards, and investigation notes.
# SOC Sysmon + Splunk Project  
## Phishing-Driven Ransomware & Lateral Movement Scenarios

---

## Project Overview
This project presents a hands-on Security Operations Center (SOC) lab focused on detecting, investigating, and correlating endpoint activity using **Windows Sysmon logs collected in Splunk**.  

The project simulates **two realistic attack scenarios** initiated through phishing and mapped step-by-step to the **Cyber Kill Chain**, demonstrating how an attacker progresses from reconnaissance to impact, and how a SOC analyst can observe, correlate, and respond to each phase using telemetry.

The main objective is to **show visibility**, **attack progression**, and **log-based investigation**, rather than offensive exploitation itself.

---

## Environment & Tools
- **Splunk Enterprise** (Search, dashboards, correlation)
- **Windows Sysmon** (process, network, persistence, and activity telemetry)
- **Windows Endpoints** ( windows virtual machine - victim ) 
- **RDP (Remote Desktop Protocol)** for attacker access ( using FreeRdp3 tool ) 
- **Honeypot-Pentbox** ( detecting the lateral movement ) 
- **phishing website** (phishing email link, harvester server for credentail stealing)
---

## Attack Scenarios Overview

### Scenario 1: Phishing → Ransomware Attack  
### Scenario 2: Phishing → Lateral Movement via RDP  

> **Important:**  
The initial attack stages (**Reconnaissance → Weaponization → Delivery**) are identical in both scenarios.  
The difference starts after initial access.

---

## Shared Initial Phases (Both Scenarios)

### 1. Reconnaissance
The attacker begins by identifying a **vulnerable employee** within the organization.  
Publicly available information is gathered from:
- Social media profiles
- Public posts and personal interests
- Work-related details that increase phishing credibility  

This information is used to craft a **targeted phishing attack** rather than a generic one.

---

### 2. Weaponization
Based on the reconnaissance results:
- A **malicious phishing link** is prepared
- A **fake website** is created to imitate a legitimate company service
- The phishing content is tailored to match the victim’s role and interests  

This stage prepares the payload but does not yet interact with the victim.

---

### 3. Delivery
The attacker sends a **spoofed email** that appears to originate from the company’s legitimate email address.

The email:
- Uses realistic formatting and wording
- Contains a malicious link
- Encourages the employee to urgently click the link  

Once the victim clicks the link, they are redirected to the fake website.

---

### 4. Exploitation
At the fake website:
- The employee enters their credentials
- Credentials are **harvested by the attacker**
- Valid corporate login information is now compromised  

Using these stolen credentials, the attacker gains authenticated access.

---

## Scenario 1: Phishing → Ransomware (Full Kill Chain)

### 5. Command & Control / Installation
Using the harvested credentials:
- The attacker connects to the victim’s machine via **RDP**
- Malicious tools are transferred
- A **ransomware payload** is installed on the victim’s device  

Sysmon logs capture:
- RDP logon behavior
- Suspicious process creation
- File execution patterns

---

### 6. Actions on Objectives (Impact)
The ransomware is executed:
- Important files on the victim’s device are encrypted
- File extensions and access behavior change
- A ransom message is displayed  

The victim is **blackmailed for Bitcoin payment** in exchange for file recovery.

From a SOC perspective, this phase shows:
- Clear destructive activity
- High-severity indicators
- Strong justification for alerting and incident response

----------------------------------

## Scenario 2: Phishing → Lateral Movement with Honeypot Detection

This scenario builds on the same initial phishing attack used in Scenario 1, but instead of immediately deploying ransomware, the attacker focuses on **lateral movement inside the network**, while a **honeypot system (PentBox)** is deployed to detect and observe malicious behavior.

---

### Shared Initial Phases
The following phases are identical to Scenario 1:
- Reconnaissance
- Weaponization
- Delivery
- Exploitation

A vulnerable employee is socially profiled, a spoofed phishing email is sent, credentials are harvested through a fake website, and initial access is obtained using valid credentials.

---

### Command & Control
Using the stolen employee credentials:
- The attacker establishes **RDP access** to the compromised workstation
- This machine becomes the attacker’s initial foothold
- Sysmon logs capture RDP-related activity, process execution, and network connections

Instead of deploying ransomware, the attacker begins exploring the internal environment.

---

### Lateral Movement
The attacker attempts to:
- Discover additional systems on the internal network(filesytem server - sql server - databse servers ) 
- Reuse credentials for access to other machines
- Move laterally using **RDP connections**

This behavior generates multiple indicators such as:
- Abnormal RDP sessions
- Repeated authentication attempts
- New process creation across hosts
- Network connections to unfamiliar internal services

---

### Honeypot Deployment (PentBox)
To detect and study lateral movement activity, a **honeypot server** is deployed using **PentBox**.
it was deployemt to mimics the filesytem server 
The honeypot:
- Mimics a legitimate internal service
- Is intentionally exposed to attract attacker interaction
- Does not host real production data (fake files) 
- Acts as an early warning system for malicious behavior

The attacker unknowingly interacts with the honeypot while scanning or attempting lateral movement.

---

### Honeypot Log Collection & Splunk Integration
All **PentBox honeypot logs** are:
- Collected on the honeypot system
- Forwarded to **Splunk**
- Indexed alongside Sysmon and host logs

This allows the SOC analyst to:
- Correlate attacker activity between real endpoints and the honeypot
- Identify suspicious connection attempts and probing behavior
- Confirm lateral movement intent even before real systems are compromised

From a SOC perspective, honeypot interaction is a **high-confidence indicator of malicious activity**, since legitimate users should never interact with it.
Can be used as an IOCs 
---

### Actions on Objectives
Rather than immediate destruction:
- The attacker focuses on expanding access
- Attempts persistence and reconnaissance across the network
- Interacts with the honeypot, revealing malicious intent

This scenario demonstrates how **lateral movement can be detected early** using a combination of:
- Sysmon endpoint telemetry
- RDP activity monitoring
- Honeypot-based deception technology
- Centralized log correlation in Splunk


## SOC Visibility & Log Analysis
Throughout both scenarios, the SOC analyst:
- Monitors Sysmon telemetry in Splunk
- Correlates events across multiple attack stages
- Identifies suspicious behavior tied to the Cyber Kill Chain phases  


Key focus areas:
- Process creation anomalies
- Network connections
- RDP activity
- Credential abuse indicators
- File modification behavior

---
## Author
Omar Ayesh – Cybersecurity Student (2026)
