# Microsoft-Sentinel
Live SOC honeypot in Microsoft Azure

This project makes use of Microsoft Azure as a cloud platform to deploy a virtual machine set up as a honeypot to gather information about brute-force login attempts worldwide. The project includes mapping the geolocation of threat actors, tuning of alerts, and application of security controls.

Guide by Phillip: <a href="https://www.youtube.com/watch?v=mOjbD7FkUUI&ab_channel=Phillip" target="_blank">Cybersecurity Lab - Building a Live SOC + Honeynet in Azure</a>

# Initial Steps and Setup
I first headed over to Microsoft Azure and created my own resource group (Dandelion) and within the resource group deployed a Windows virtual machine. This Windows virtual machine will be the honeypot and therefore has an inbound port rule for any and all traffic. This makes the virtual machine exposed to the world and vulnerable. 

![image](https://github.com/user-attachments/assets/27a584ce-1fb6-450e-8aaa-47b2bb2514bc)
![image](https://github.com/user-attachments/assets/42d63ff5-5a11-4eef-8cae-b5dc927a2895)

Once the virtual machine is deployed, I connected through RDP and disabled Windows Defender Firewall which allows RDP connections from other machines and locations. After that, I installed SQL Server 2019 and SQL Server Management Studio (SSMS) on the virtual machine.

![image](https://github.com/user-attachments/assets/7afeff67-d423-4ec4-9711-d3ff9bdb1798)
![image](https://github.com/user-attachments/assets/f1636b1b-e1a9-4653-9d9f-bd1cb44224ed)

With the SQL server installed, I logged in and configured both the successful and failed logins to be sent to event viewer. Next, I created a watchlist wizard in my analytics workspace within Microsoft Azure. The watchlist wizard enables the mapping of geolocations based on network IPs.

![image](https://github.com/user-attachments/assets/32f5459d-bbc8-418a-8d5c-2e36a6dc7d7e)

After that, I created a storage account for my resource group Dandelion which will be filled with logs from the honeypot. To send the logs from the honeypot into the storage account, I created a flow log.

![image](https://github.com/user-attachments/assets/b59c38ad-2e88-463f-a1ad-d5d93ac86634)

I also added a data collection rule which will collect the logs from the Windows machine. Following the guide, I copy pasted two queries; one for Windows Defender Malware Detection and the other for Windows Firewall Tampering Detection.

![image](https://github.com/user-attachments/assets/fb242fb8-7682-472d-9bef-b51c609321af)

Then I configured the diagnostic settings which specifies which log categories should be sent to the logs analytic workspace. I configured a total of three diagnostic settings for users, monitor, and storage account. For storage account, I enabled the blob diagnostic setting which collects and routes logs from Azure Blob storage to the logs analytics workspace. After configuring the diagnostic settings, I created a key vault which stores keys on the cloud. I then created a key to test for an alert as well as to simulate real-world infrastructure.

![image](https://github.com/user-attachments/assets/e42c952e-2967-41bb-b7d3-2ead9a9e1462)

Next, I deployed three workbooks in the Microsoft Sentinel Log Analytics workspace which visualize the data from the logs.

![image](https://github.com/user-attachments/assets/67011156-767a-4d6f-b6a9-d6af2a7d31e0)
![image](https://github.com/user-attachments/assets/238bc3e6-60e1-4ea9-8f81-a7b78c0fb613)
![image](https://github.com/user-attachments/assets/c97f6248-d357-4b69-9dbf-638bf6503ee2)

Now that the logs have been setup to correctly route to the log analytics workspace for analysis, it's time to harden the system. First, I changed the allow all firewall rule exposing the virtual machine to the internet into block all external RDP connections from the internet. Instead of RDP, I will use Bastion which is a secure way to connect to the virtual machine without having to expose the virtual machine to the internet which is a significant security risk due to brute force attempts. Then I re-enabled Windows Defender Firewall in the virtual machine. I then follow the following recommendations to further harden the machine and return the next day to see the security improvements.

![image](https://github.com/user-attachments/assets/d67bdd2e-edb0-4a1b-aa7e-ef26af2b3d62)
