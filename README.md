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

   ![1](https://github.com/user-attachments/assets/cd2544b8-c502-4ac0-a3f8-a18f6b6e6e29)

3. Click on the "Create" button to start setting up your VM.

   ![2](https://github.com/user-attachments/assets/71772dd8-0734-4d78-9659-a2344820407c)

4. In the creation form:
    - Create a new resource group named `Honeypotlab`.
    - Name the VM `honeypot-vm`.

      ![3](https://github.com/user-attachments/assets/20bff81c-fd75-48dd-bd3d-4806e3776339)

    - Select the region.
    - Select Windows 10 pro for image.
    - Set up a username and password for the VM login.
    - Confirm the licensing terms.
5. Proceed to the Disks configuration by clicking "Next: Disks >".

   ![4](https://github.com/user-attachments/assets/f4d8504b-d1b9-4b0b-9dc7-9f5862d521c5)

6. Move to the Networking configuration by clicking "Next: Networking >".

    ![5](https://github.com/user-attachments/assets/4525cc83-bb32-4c00-b4e9-9a1196312fd8)

    - Choose "Advanced" under the Network Security Group (NSG) settings.

      ![6](https://github.com/user-attachments/assets/d33d01ff-db26-42d2-9c12-f89b1b89f61f)

    - Create a new NSG with rules allowing all inbound traffic; think of it as a firewall that is open to the public internet:
        - Remove the default rule. We will create our own inbound rule that allows all traffic into the VM.
        - Set Source and Destination Port Ranges to `*`.
        - Set the priority to `100` (low priority to allow all traffic).

          ![7](https://github.com/user-attachments/assets/780916ae-acec-4aa2-a231-6e7c116007bb)

7. Review and finalize the VM creation by clicking "Review and create" and then "Create".
8. Wait for the VM to deploy.

  ![8](https://github.com/user-attachments/assets/c568dfb6-24bb-4e21-9a9a-b8c68722e8c6)

**Note**: Deployment of your VM will take some time, so we will proceed with the next steps in the lab.

## Step 3: Create a Log Analytics Workspace

The purpose is to ingest logs from the virtual machine. We will ingest Windows event logs and create custom logs that contain geographic information to discover where the attackers are coming from.
1. Navigate to the Azure portal and go to the Log Analytics Workspaces section, or you can find it by searching for "Log Analytics Workspaces" in the search bar.

   ![9](https://github.com/user-attachments/assets/d1fd19a0-9cca-4823-a4ca-90ac4e4aefb4)


2. Click on "Create log analytics workspace." This is where those logs will be stored, and our SIEM will connect to this workspace to display the geographic data.

   ![10](https://github.com/user-attachments/assets/db7cc1cb-9840-4443-b634-3811c9ed84db)

    - Select the resource group `Honeypotlab`.
    - Name the workspace `log-honeypot`.
    - Choose the same region as the virtual machine.

      ![12](https://github.com/user-attachments/assets/9bd9197a-c409-42f6-863b-824efde0f628)

4. Review and create the workspace by clicking "Review and create" and then "Create."
5. Wait for the deployment to complete.

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
