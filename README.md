# Azure Sentinel tutorial MAP with Honeypot and Live Cyber Attacks

This guide provides a step-by-step walkthrough for setting up an Azure Sentinel project with a Windows machine exposed to the internet as a honeypot. The goal is to monitor, detect, and analyze potential security threats.
We will extract the IP addresses of users attempting to log into our VM. These IP addresses will be queried using a geolocation API site to determine their geographical location. Using this information, we will create custom logs that include longitude, latitude, and country data. These logs will then be sent to the Log Analytics workspace in Azure. By configuring our SIEM (Security Information and Event Management) system, we can visualize and plot the attackers' locations on a map.



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

    - Choose "Advanced" under the NIC Network Security Group settings.

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

3. Review and create the workspace by clicking "Review and create" and then "Create."
4. Wait for the deployment to complete.

  ![13](https://github.com/user-attachments/assets/5081e440-1c1a-4b69-9c68-7dba3a867ba7)

## Step 4: Enable Log Collection in Microsoft Defender for Cloud
1. Navigate to Microsoft Defender for Cloud in the Azure portal.

   ![14](https://github.com/user-attachments/assets/883ab258-1772-428e-9a5f-09103d46de71)

2. Go to "Environment Settings".
 
  ![15](https://github.com/user-attachments/assets/82601231-74c6-43b7-ba35-7c256b0edb17)

3. Click on the down arrow next to "Azure Subscription 1". You will then see our lab's analytics workspace named "log-honeypot". Select it.

   ![16](https://github.com/user-attachments/assets/071333a9-ee3d-4bb4-9d2f-320bf9b79417)

4. Enable the Servers plan. Then save it.

   ![17](https://github.com/user-attachments/assets/9eb404ed-ed15-4add-88b8-c0136ab1bd45)

5. Click on "Data collection" on the left side.

6. Set data collection to "All Events" and save the settings.

    ![18](https://github.com/user-attachments/assets/29b75eeb-fa9b-4623-b99a-7b6102d41d24)


## Step 5: Connect the VM to the Log Analytics Workspace
1. Return to the Log Analytics Workspaces section in the Azure portal.
2. Select the workspace "log-honeypot".

   ![19](https://github.com/user-attachments/assets/882f5a96-17ba-4fef-b2d4-ceb31d9f515e)

4. On the left, Click on "Virtual Machines (deprecated)" to connect your VM.

    - Select your VM `honeypot-vm`.

      ![21](https://github.com/user-attachments/assets/25921487-2c43-4daf-b289-150cdd5b9518)

    - Then click Connect.

      ![22](https://github.com/user-attachments/assets/d3970339-f003-4ff3-aea2-2ed812954d66)

6. Wait for the connection to complete.


## Step 6: Set up Microsoft Sentinel

1. Go to the Azure portal and select Microsoft Sentinel.
2. Click on "Create".
   
 ![23](https://github.com/user-attachments/assets/e8d27092-edf8-4110-b430-069e8b5c357a)

3. Select the log analytics workspace we created (`log-honeypot`) and add it.

   ![24](https://github.com/user-attachments/assets/922745df-4cbf-435b-a623-468b99816422)


## Step 7: Configure the environment within the VM

1. Navigate to "Virtual Machines".
2. Select our VM.

   ![25](https://github.com/user-attachments/assets/b75e2d76-d5a5-42c8-8952-657417cd9174)

3. Copy our public IP address; we will use it to log in via Remote Desktop.
4. On your Windows machine, go to the Start menu and type "Remote Desktop". Open the Remote Desktop application and paste the public IP address into the designated field. Then hit connect

   ![26](https://github.com/user-attachments/assets/426eb69f-124b-470f-bfe0-1c98ada88132)

5. You will see a place to enter credentials. Ignore the credentials that you see there and click on "More choices" at the bottom. Then, click on "Use a different account"
6. Enter the username and password that you created while setting up the VM.
7. The screen will then prompt you to log into your VM. Next, you will see a privacy settings screen. Toggle all settings off.
8. Click "Accept" to proceed into your VM environment.
9. When prompted regarding the network settings in your VM, click "Yes".

Next, we will disable the firewall to allow ICMP traffic from the internet, making our system discoverable.
