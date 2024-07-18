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

#### Disable the Windows Firewall

Next, we will disable the firewall to allow ICMP traffic from the internet, making our system discoverable.

1. On your Windows VM, go to Windows Defender Firewall.

    ![31](https://github.com/user-attachments/assets/f0bc6b02-a78c-4927-98aa-643725619b2c)

2. On the left side, click "Turn Windows Defender Firewall on or off".
3. Turn off Windows Defender Firewall for all profiles.

   ![30](https://github.com/user-attachments/assets/337c8e6f-f434-48e2-82a8-b1d1dfadf952)

## Step 8: Download the IPGeo PowerShell script

The [IP Geolocation](https://ipgeolocation.io) is the website we will use to gather our geolocation data.

1. To get the PowerShell script, head over to the following [GitHub repository](https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1?source=post_page-----45a67e20d1cb--------------------------------).
2. Select the full script with `CTRL+A` and then copy it with `CTRL+C`.
3. On your Windows VM, go to the bottom left search bar and type `PowerShell ISE`. Open PowerShell ISE with administrative privileges from the search results.
4. Click on the white page icon to open a new script page.
  
    ![32](https://github.com/user-attachments/assets/6a41dcf7-d711-436d-a227-733d4e7a9675)

5. Paste the Powershell script.

   ![33](https://github.com/user-attachments/assets/5fb16630-e627-40e6-b9c9-512b62f179aa)

6. Go to [IP Geolocation](https://ipgeolocation.io/) and create a free account to get API access.
7. Once you sign up and verify your account, log in to retrieve your API key. Copy your API key.
8. In your PowerShell script, locate the existing API key.
9. Delete the placeholder key and paste your API key in its place.

    ![34](https://github.com/user-attachments/assets/b9e4310c-57c9-4d98-b6e2-49d70558c49e)

11. Save it on Desktop as Log_Exporter
12. Now run the script

    ![35](https://github.com/user-attachments/assets/0fc99c13-2498-46e9-bddb-00ff95f6e35e)


    ![37](https://github.com/user-attachments/assets/d56d1b5d-43c2-4c3f-9329-74e0db2e9e5d)

The script scans security logs for Event ID 4625 (failed login events), extracts the IP addresses involved, fetches their geolocation data, and compiles this information into a new log file saved as "failed_rdp" in C:\ProgramData.
 
    ![36](https://github.com/user-attachments/assets/baf43b83-c39b-4bc2-b855-0d14a5d8d024)

## Step 9: Create Custom Log with Geo Data in Log Analytics Workspace

1. Navigate back to the Azure portal and search for "Log Analytics Workspaces".
2. Select your workspace from the list.
3. In the left-hand menu, click on "Tables".
4. Create a new custom log (MMA-based).

   ![38](https://github.com/user-attachments/assets/bc778857-6761-43fa-8d7a-9c918faf7a47)

5. Since the log is on our VM machine, we will copy the contents of "failed_rdp" located in C:\ProgramData to a file on our host machine. Save it as failed_rdp.log
6. Add the file.

   ![39](https://github.com/user-attachments/assets/a6426b2e-178f-4cb2-aed3-85541b13acb1)

7. Select Next.
8. This is how it should look. If it's right, click 'Next'.

   ![40](https://github.com/user-attachments/assets/7218ebd4-3255-44e3-ac95-bf946b0578fb)

9. Now, add the file path where the data resides on your VM: `C:\ProgramData\failed_rdp.log`.

   ![41](https://github.com/user-attachments/assets/f8c76311-5a11-4b17-bcdd-cbd753d7bdd5)

10. Name your custom log and clikc next.

    ![42](https://github.com/user-attachments/assets/cf0c327d-21e3-4fef-8a02-0c405d59733c)

11. Create the custom log.
12. Now navigate to your Log Analytics workspace.
13. Go to `Logs`.
14. In the query editor, type `FAILED_RDP_WITH_GEO_CL`.
15. Click the blue Run button.
16. Wait a moment for the results to load. This will return our sample logs with geolocation data.

   ![43](https://github.com/user-attachments/assets/7619b8bd-f7b9-4f7a-88e0-8d0704763c56)

## Step 10: Extract and Categorize Geo-data from Sample Logs

1. Take a look at our sample logs in FAILED_RDP_WITH_GEO_CL.
2. In the RawData columns, you'll find information like longitude, latitude, destination host, etc. We need to categorize these values from the raw data before obtaining geolocation data.
3. Right-click the first log you see in the search results and click "Extract Fields from `FAILED_RDP_WITH_GEO_CL`".
4. Under Main Example, highlight the latitude value (not the word ‘latitude’ itself). A window will automatically pop up.
5. Under Field value, type ``latitude`` and under Field type, choose ``numeric``. Click ``Extract``.
6. On the right, check that the SIEM is selecting the correct values on each sample log.
7. Click ``Save extraction``.

