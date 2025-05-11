# SOAR-EDR Automation Framework

## Project Overview
This repository houses a detailed Security Orchestration, Automation, and Response (SOAR) integration with an Endpoint Detection and Response (EDR) system, aimed at automating the detection, notification, and response to security incidents. The project utilizes LimaCharlie as the EDR platform to monitor and detect threats, Tines as the SOAR platform to orchestrate automated workflows, Slack for real-time team alerts, Gmail for email notifications, and a Windows Server 2019 instance hosted on Vultr as the target environment. The primary focus is on detecting the "lazagne" hack tool—a credential-dumping utility often used maliciously—and automating the response process, including user prompts and machine isolation.

The solution provides a practical blueprint for enhancing incident response efficiency, reducing manual effort, and validating automation through network isolation tests. Whether you're a security professional or a developer, this project offers valuable insights into EDR-SOAR integration and API-driven automation.

## Tools and Technologies
- **LimaCharlie**: Cloud-based EDR platform for threat detection and sensor management.
- **Tines**: SOAR platform for workflow automation and orchestration.
- **Slack**: Real-time communication tool for team notifications.
- **Gmail**: Email service for additional notification delivery.
- **Windows Server 2019**: Operating system on Vultr for hosting the target environment.
- **Vultr**: VPS provider for deploying the Windows Server instance.

## Prerequisites
Before getting started, ensure you have the following:
- A Vultr account to provision a Windows Server 2019 instance.
- A LimaCharlie account with organization and API key access.
- A Tines account with webhook and integration capabilities.
- A Slack workspace with an "alerts" channel and app credentials (client ID, secret, token).
- A Gmail account for email notifications.
- Administrative access to the Windows Server for sensor installation.
- Basic knowledge of JSON configuration, API usage, and network troubleshooting.

## Setup Instructions
Follow these detailed steps to replicate the project:

### 1. Provision the Windows Server
- Sign up at Vultr and create a Windows Server 2019 instance.
- Connect via Remote Desktop Protocol (RDP) and configure the server with administrative privileges.

### 2. Configure LimaCharlie
- Log into your LimaCharlie account and create a new organization.
- Generate an installation key from the LimaCharlie dashboard under "Installation Keys."
- Download the LimaCharlie agent installer and execute it on the Windows Server using the command: `LimaCharlieAgentInstaller.exe --install <your_installation_key>`.
- Verify the sensor’s online status in the LimaCharlie Sensors dashboard.

### 3. Set Up Slack and Gmail
- Create a free Slack workspace and an "alerts" channel.
- Generate a Slack app with bot token and OAuth credentials for Tines integration.
- Configure a Gmail account in Tines for email notifications, ensuring proper SMTP settings.

### 4. Configure Tines
- Sign up for Tines and create a new story to house the automation logic.
- Add a webhook in Tines to receive detection events from LimaCharlie.
- Integrate Slack and Gmail in Tines using their respective credentials.

### 5. Create Detection Rule in LimaCharlie
- In LimaCharlie, create a JSON-based detection rule targeting "lazagne.exe" events (e.g., file path: "C:\Users\Administrator\Downloads\lazagne.exe", event type: NEW_PROCESS).
- Test the rule by running `lazagne.exe` on the Windows Server and confirm detection in the LimaCharlie event log.

### 6. Build the Tines Playbook
- In Tines, create an event transform to parse LimaCharlie detection data (title, time, hostname, IP, etc.).
- Add Slack and Gmail actions to send notifications with detailed fields.
- Implement a user prompt asking, "Do you want to isolate the machine?" with Yes/No options.
- Set up conditional triggers: No sends a Slack message ("Computer [hostname] was not isolated, please investigate"), Yes initiates isolation.
- Configure an HTTP request to LimaCharlie’s API with the organization API key to isolate the machine using the sensor ID.
- Add a follow-up HTTP request to retrieve and report the isolation status via Slack.

### 7. Validate the Solution
- Trigger a detection by running `lazagne.exe` and follow the prompt.
- Use the Windows Server’s command prompt to ping an external site (e.g., youtube.com) before and after isolation.
- Check the LimaCharlie sensor timeline and Slack messages to confirm isolation status.

## Usage
To use the framework:
1. Deploy `lazagne.exe` on the Windows Server to simulate a threat.
2. The Tines playbook will:
   - Send a Slack message and Gmail email with detection details (title, time, hostname, IP, username, file path, command line, sensor ID, detection link).
   - Display a Tines prompt for isolation decision.
   - If No, post a Slack message: "Computer [hostname] was not isolated, please investigate."
   - If Yes, isolate via LimaCharlie’s API and update Slack: "Computer [hostname] has been isolated, isolation status: [true/false]."
3. Validate isolation with ping tests and LimaCharlie dashboard checks.

## Expected Outcomes
- LimaCharlie detects the "lazagne" hack tool in real-time.
- Tines orchestrates Slack and Gmail notifications with comprehensive event data.
- User prompt in Tines enables manual oversight before action.
- Automated isolation via LimaCharlie’s API, confirmed by Slack updates.
- Failed ping tests post-isolation on Windows Server, validating network isolation.

## Troubleshooting
- **LimaCharlie Sensor Offline**: Ensure the installation key is correct and the Windows Server has internet access. Restart the sensor service if needed.
- **Tines Webhook Not Triggering**: Verify the LimaCharlie webhook URL and test connectivity.
- **Slack/Gmail Notifications Failing**: Check credentials and channel/email settings in Tines.
- **Isolation Not Working**: Confirm the LimaCharlie API key and sensor ID are correctly configured in Tines.
