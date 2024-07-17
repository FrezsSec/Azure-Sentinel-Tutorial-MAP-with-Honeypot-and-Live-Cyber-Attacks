# Azure Sentinel tutorial MAP with Honeypot and Live Cyber Attacks

This guide provides a step-by-step walkthrough for setting up an Azure Sentinel project with a Windows machine exposed to the internet as a honeypot. The goal is to monitor, detect, and analyze potential security threats.

## Step 1: Create an Azure Subscription
1. Go to [Azure's official website](https://azure.microsoft.com/en-us/) and create a free Azure account.
    - The account setup is free.
    - You receive $200 in free credits for exploring Azure services.
    - A credit card is required for setup, but the lab should be covered by the free credits.

## Step 2: Create a Virtual Machine (VM)
1. Log in to the [Azure Portal](https://portal.azure.com).
2. Navigate to the Virtual Machines section by clicking on the Virtual Machines icon or using the search function.
3. Click on the "Create" button to start setting up your VM.
4. In the creation form:
    - Create a new resource group named `Honeypotlab`.
    - Name the VM `honeypot-vm`.
    - Select the region (e.g., West US 3).
    - Set up a username and password for the VM login.
    - Confirm the licensing terms.
5. Proceed to the Disks configuration by clicking "Next: Disks >".
6. Move to the Networking configuration by clicking "Next: Networking >".
    - Choose "Advanced" under the Network Security Group (NSG) settings.
    - Create a new NSG with rules allowing all inbound traffic:
        - Set Source and Destination Port Ranges to `*`.
        - Set the priority to `100` (low priority to allow all traffic).
        - Name the rule (e.g., `DANGER_ANY_IN`).
7. Review and finalize the VM creation by clicking "Review and create" and then "Create".
8. Wait for the VM to deploy.

## Step 3: Create a Log Analytics Workspace
1. Go to the Azure portal and navigate to the Log Analytics Workspaces section.
2. Click on "Create log analytics workspace."
    - Select the resource group `Honeypotlab`.
    - Name the workspace `honeypot-lab1`.
    - Choose the same region (e.g., West US 3).
3. Review and create the workspace by clicking "Review and create" and then "Create."
4. Wait for the deployment to complete.

## Step 4: Enable Log Collection in Microsoft Defender for Cloud
1. Navigate to Microsoft Defender for Cloud in the Azure portal.
2. Go to "Environment Settings" and select your Azure subscription (e.g., Azure Subscription 1).
3. Enable log collection for the workspace `honeypot-lab1`.
4. Ensure that "Servers and SQL Servers" is enabled to allow event logging.
5. Set data collection to "Collect All Events" and save the settings.

## Step 5: Connect the VM to the Log Analytics Workspace
1. Return to the Log Analytics Workspaces section in the Azure portal.
2. Select the workspace `honeypot-lab1`.
3. Click on "Virtual Machines (deprecated)" to connect your VM.
    - Select your VM (e.g., `honeypot-vm`) and click "Connect."
4. Wait for the connection to complete.

## Summary
By following these steps, you will have set up an Azure Sentinel environment with a honeypot VM exposed to the internet. This configuration allows you to monitor and analyze potential security threats, providing valuable insights into attack patterns and helping you enhance your security posture.
