# Logging and Monitoring

## Objectives for the next 7 Labs that will create our Logging and Monitoring System.

- Azure Logging at Different Layers (Tenant, Activity, Resource)
- Geo IP Data Ingestion & Log Analytics & Microsoft Sentinel Setup
- Enable MDC & Configure Log Collection for VMs
- KQL Deep Dive
- Tenant Level Logging
- Subscription Level Logging
- Resource Level Logging

### Environments and Technologies Used:

- Microsoft Azure
- Microsoft Sentinel
- Microsoft Cloud Defender

### Operating Systems Used:

- VM Windows 10 PRO (21H2)
- VM Linux Ubuntu 20.12
<details close>

<div>

</summary>

#### In this lab, we will use 2 GeoIP files which will help us correlate IP addresses to figure out where the attacks originated from. <b>

> This will be interesting because in Event Viewer we only see IP addresses, but we have no clue where the attacks came from.``` 

- We will first download Two IP files 

![image](https://user-images.githubusercontent.com/112146207/231026643-50acaa3b-3f83-4467-b6ee-49cf28ee2225.png)

- These files are over 500MB, here is a link to download them: 

- We will then go into our Azure account and search "storage account" and click on "Create storage account"

![image](https://user-images.githubusercontent.com/112146207/231028070-1e083867-7a13-4d6f-b4d7-a68c387c0ee5.png)

- When we create our storage account make sure to put it under the "RG-Cyber-Lab" resource group, make a storage account name, change the region, and change the redundancy to locally-redundant storage (LRS).

- Remember that Redundancy in cybersecurity is often used to improve fault tolerance, which means the ability of a system to continue functioning even if a part of it fails.

![image](https://user-images.githubusercontent.com/112146207/231028783-ea7691e8-3f53-4533-852d-4e86d36b566c.png)

- Once the storage account is created, go to the search bar and type in "storage account" 
> Within the storage account, create a container named “ipgeodata”

![image](https://user-images.githubusercontent.com/112146207/231030560-acfa43e5-4d3e-4319-becb-5e9bf0dd42df.png)

- Click on ```ipgeodata and we will upload the 2 files we downloaded
> This might take a while since one of the files is very large, but patience is key! 

![image](https://user-images.githubusercontent.com/112146207/231031297-24b3618d-59af-49ae-b564-7397895b5cb9.png)

- We now need to create SAS URLs for both of these files.
> A SAS is used to provide access to files on an individual basis instead of opening up the whole container.

- We will first copy the file names and jot them down on a notepad 

![image](https://user-images.githubusercontent.com/112146207/231032762-02446bd7-f2ae-4378-acea-31c8029c4083.png)

- Right-click the file name and press "generate SAS" and make sure it is the city block IPv4 

![image](https://user-images.githubusercontent.com/112146207/231033022-3a06ba97-7561-408a-9fb0-cd340e02744d.png)

- Change the expiration date and click "generate SAS" then copy the Blob SAS URL and put it on your notes because we will use it later

``` https://fin69.blob.core.windows.net/ipgeodata/GeoIP2-City-Blocks-IPv4.csv?sp=r&st=2023-04-20T08:21:40Z&se=2024-04-20T16:21:40Z&spr=https&sv=2021-12-02&sr=b&sig=RsJoMHzVf0j1UwvGVISb1hVOWnpoT%2BIo5roRwZJcQsM%3D```

```https://fin69.blob.core.windows.net/ipgeodata/GeoIP2-City-Locations-en.csv?sp=r&st=2023-04-20T08:22:54Z&se=2024-04-20T16:22:54Z&spr=https&sv=2021-12-02&sr=b&sig=usl0HXJnadp8mnD7TxrrFcTJ34T8qddjZY%2Fft63Q1js%3D```

- Just to explain why we are doing this, we are going to give this SAS URL to the Log repository which will read all this data in Sintenial and put it into its database. Look forward to that, shortly. 

![image](https://user-images.githubusercontent.com/112146207/231033594-d39d112c-3a55-4bc2-9a05-e3336d00f910.png)

- The same steps will be done for the "city-locations" file

![image](https://user-images.githubusercontent.com/112146207/231034805-cc50ffd3-0c0b-4862-959b-ec9c1275a094.png)

- It's very important that you copied the Blob URLs to a notepad because we will use them

![image](https://user-images.githubusercontent.com/112146207/231035113-37153ffd-7b20-4039-89e0-32fa03063cd6.png)

![LOGGING AND MONTIROING PROG](https://user-images.githubusercontent.com/109401839/233307421-5f4df4ab-a0ce-4f64-a485-891587567414.png)

- Our SIEM will look into our work analytics workspace where it will collect, analyze and identify logs in real-time. 

- Go to portal.azure.com and search "log analytics workspace" then click "create"

> Enter your resource group, name, and region
> Click "create"

![image](https://user-images.githubusercontent.com/112146207/231041157-07ff8690-0e94-4d1a-8855-b0a13f2ae6ca.png)

- We just created our work analytics workspace which will be injected with Geo data to let us correlate IP addresses and see people's origins. We will then create our SIEMs resource and connect it to the log analytics workspace. 

- Go to the search bar and search "Microsoft Sentinel" and hit "Create" 

- Then click on your workspace and click "add" 

![image](https://user-images.githubusercontent.com/112146207/231042846-4b76998a-cc03-4c4c-9568-174d36763b7b.png)

- We will now create 2 Watchlists within Azure Sentinel and ingest geo-data CSV Files from Azure Storage

![image](https://user-images.githubusercontent.com/112146207/231043926-67b9a1bc-b5cf-4b74-81bc-2370a8901560.png)

- Now add this information exactly and use YOUR Blob URLs that you copied and saved 

![image](https://user-images.githubusercontent.com/112146207/231044304-b37eee42-396e-4d38-a3b7-24f2d6062360.png)

![image](https://user-images.githubusercontent.com/112146207/231045193-e4b86e10-1d9f-43a7-98da-d447ddcb1955.png)

![image](https://user-images.githubusercontent.com/112146207/231052913-3c103c38-3ee6-4f4c-9efa-8d3832d7252a.png)

- For the second watchlist, I put this information in

![image](https://user-images.githubusercontent.com/112146207/231053907-bdec4f66-2204-4310-8cd4-0ead229d4c39.png)

- Now we have to allow these files to “upload”/load from our storage account into Sentinel/Log Analytics Workspace. The big one will likely take over 24 hours

- We will go to the work analytics workspace and query the watchlists we created just to make sure we can see the records from both watchlists 

- It should look something like this:

![image](https://user-images.githubusercontent.com/112146207/231058252-685c7062-26c7-427d-970d-34c9fe2d851a.png)
<details close>

---

</summary>

In this section, we will create a Linux VM, and we're going to configure the Windows security event logs from our Windows machine, and the syslog logs from our Linux machine to send to our log analytics workspace. In addition to the VM, we will also configure logging for the NSG (AKA the mini firewalls) and we're going to send flow logs into the log analytics workspace. 

To create another Virtual Machine in Azure, use the same Region, Resource Group, and VNet as the previous VM, and name it "linux-vm". 
Avoid choosing B1s for the VM size as it may stop creating logs during a DDoS attack. 
Use a username and password for authentication instead of SSH keys to restrict access. 
Lastly, open up the NSG to all traffic to allow for inbound and outbound traffic control.
  
![image](https://user-images.githubusercontent.com/112146207/231915269-71d8b02a-9a26-4606-80a6-15b9e2dbfb3e.png)
![image](https://user-images.githubusercontent.com/112146207/231915347-62f9b7dc-8417-407e-8c89-868c9030add0.png)


---
 
To enhance the security of your Azure environment, there are three steps that we will take. 
First, enable Microsoft Defender for Cloud for your Log Analytics Workspace. 
 
![image](https://user-images.githubusercontent.com/112146207/231920040-942a930e-8b64-4e57-a570-1c061285996c.png)
   
![image](https://user-images.githubusercontent.com/112146207/231926363-08254f1f-16cb-4063-b121-98b233bd2e3c.png)
 
Make sure everything is checked off and that you have your resource group, subscription, and your log analytics workspace info.
![image](https://user-images.githubusercontent.com/112146207/231927395-28eb3a69-932b-4460-8cad-6641401e2528.png)

We're going to go ahead and enable the security policy since we will use it later on

Make sure to click on "security policy" and then click "Add more standards"

![image](https://user-images.githubusercontent.com/112146207/232256225-219fdb5c-777a-452f-bdc3-7b4b71149ba1.png)

We're going to add NIST 800-53: Security and privacy controls and Azure CIS 1.4.0 (latest version)

We will now enable Defender Plans for both VMs and SQL Instances on VMs to detect and respond to potential security threats
  
Now go back to Microsoft Defender for Cloud and click on "environment settings" and we're going to do the same thing for our work analytics workspace
  
![image](https://user-images.githubusercontent.com/112146207/232257863-6e58db5c-69af-45f5-83cb-f5344f04ce41.png)

![image](https://user-images.githubusercontent.com/112146207/232257971-c3b108d8-5904-4084-ad61-7a53d68eda18.png)

This is from the Windows event log, so we are configuring this to send security events to our log analytics workspace

![image](https://user-images.githubusercontent.com/112146207/232257982-e4af1d6f-14eb-4a93-9ea6-f49c6a99f7aa.png)
 
Now we will configure logging and log forwarding for our NSG (mini firewall) 
  
We will start by going to our Azure home page, clicking "Windows VM", then going to networking,
click on "windows-vm-nsg" 
  
![image](https://user-images.githubusercontent.com/112146207/232265995-760bf78d-f492-4d34-baaf-4651c0dc47c7.png)

We will now create some NSG flow logs 
  
![image](https://user-images.githubusercontent.com/112146207/232343530-137bf329-4e04-4045-b763-f99d2fb0246a.png)

We will do the same thing for our Linux VM 
  
![image](https://user-images.githubusercontent.com/112146207/232343651-7cad24f6-e116-4672-ae02-af80285dd4ec.png)
<details close>

---

</summary>
 
In this section, we will enable diagnostic settings for both NSGs 
  
Search "VM", click "Windows-VM" go to networking and click on your network security group 
Click on "diagnostic settings" and "add diagnostic setting" 
Put in your information and click "save" 

![image](https://user-images.githubusercontent.com/112146207/232344379-94128f13-b60a-4ea2-9ddd-75138f189657.png)

Do the same process for the Linux-VM
  
We will now add data connectors to our VMs and create some data collection rules 
  
First, go to Sentinel and click on "data connectors" and search "windows" 

You should be able to see "Windows security events via AMA"

Then click "open connector page" 

> What is Data Connectors? You can stream all security events from the Windows machines connected to your Microsoft Sentinel workspace using the Windows agent. This connection enables you to view dashboards, create custom alerts, and improve investigation. This gives you more insight into your organization’s network and improves your security operation capabilities.

![image](https://user-images.githubusercontent.com/112146207/232345194-b5cccdab-a8b2-482b-9f6b-ab2cfa2c7762.png)

Click "create data collection rule" 

This allows events/logs to be brought into the log analytics workspace from our VMs

Fill in the information 

![image](https://user-images.githubusercontent.com/112146207/232345533-3e5fe29a-37f0-42c9-ac76-0c3d84546730.png)

Go to resources and click "add resources" 
  
![image](https://user-images.githubusercontent.com/112146207/232345592-00326467-8e19-4e76-a2ba-132f0819b762.png)

And at the end, it should look like this 

![image](https://user-images.githubusercontent.com/112146207/232345624-34d89b98-2405-4606-ad08-50b6a0ff32d1.png)

We will now do this for our Linux VM
  
Search "log analytics workspace" and click on "agents"
 
Click on "Linux servers" and click "Data collection rules" 
  
![image](https://user-images.githubusercontent.com/112146207/232345917-8ff52ffd-9f89-4769-8fcb-c25e2acb3b30.png)

Click "Create" 
  
![image](https://user-images.githubusercontent.com/112146207/232345993-dff68917-0376-4073-b1ef-eb6c80c3eac6.png)

Now go to resources and click on the Linux-VM

![image](https://user-images.githubusercontent.com/112146207/232346046-56e725ef-55e0-45e5-8dc1-fb7ff2e326be.png)

We will now add a data source for our Linux VM
  
The data source type is "Linux Syslog" and leave LOG_AUTH set to LOG_DEBUG

The rest of the logs should be "none"
  
![image](https://user-images.githubusercontent.com/112146207/232346488-8bd5f1a3-5b25-4bbd-b8cb-48dcc902debb.png)

The final result should look something like this 
  
![image](https://user-images.githubusercontent.com/112146207/232348779-32801522-00c0-4f07-a9b8-1d50ea834278.png)
 
We will go back to our log analytics workspace and create another window to make sure it's collecting application logs 
  
![image](https://user-images.githubusercontent.com/112146207/232349001-7d3135e3-2d2d-42ed-a3b9-2c7cc9b65f60.png)

The final result should look like this

![image](https://user-images.githubusercontent.com/112146207/232349296-8e3bd95d-0dc4-4d8a-b597-3bb9363dc859.png)

Now, we will keep checking/ refreshing the log analytics agents tab and ensure the VMs show up there 
  
Go to "log analytics workspace" and then "agents" and make sure that your Windows and Linux VMs are showing up 
  
![image](https://user-images.githubusercontent.com/112146207/232349589-a0925f87-7c29-4b37-aa07-d1289f503c3e.png)

![image](https://user-images.githubusercontent.com/112146207/232349604-fbb882c4-332c-44d1-a4f2-ed38879289e5.png)

Type in "syslog" and click "run" and you should be able to see your logs coming in 
  
![image](https://user-images.githubusercontent.com/112146207/232672859-5c5ad373-9d24-42e6-a114-ee59c9e99ac6.png)
  
> We are now exploring KQL which is very similar to SQL 

> KQL helps us filter through logs to show us exactly what we want to find 
 
![image](https://user-images.githubusercontent.com/112146207/232673231-f23821d7-0627-43ed-80b9-5d0c7980ddc4.png)

- We will log in to our attack VM and fail a couple of logins against the Linux and Windows computers and observe them in the log analytics 
  
> Get the public IP address of your Windows VM

> Go to SSMS 

> Fail to login 3 times 

![image](https://user-images.githubusercontent.com/112146207/232674032-a4f854b3-7946-4d24-9b34-e2b16b10a153.png)
  
- Now fail to login an RDP 
  
![image](https://user-images.githubusercontent.com/112146207/232674353-95d1b883-5f04-42f4-a907-16f79cdbf617.png)
 
  We will now fail login 3 times for our Linux machine and 1 successful connection 

![image](https://user-images.githubusercontent.com/112146207/232672245-53df1c87-5d0b-4a2c-9917-00bd40a845ee.png)

We can now go to "log Analytics" 

The KQL query will look at the SSMS Authentication logs on the Windows computer

We can see that the IP address is that of the attack VM
  
![image](https://user-images.githubusercontent.com/112146207/232676039-4441b94f-6f57-4e85-9d65-ae3c1c8bb345.png)
  
We will check our Linux failed authentication attempts 
  
We can see the times I tried to log in using an incorrect user and password 
  
![image](https://user-images.githubusercontent.com/112146207/232676715-cb74bdf0-52a7-46d6-af05-ed6267308bfd.png)

![image](https://user-images.githubusercontent.com/112146207/232676902-4c080307-0cbf-49ee-b609-7bd7309dc3e1.png)

We will now check the failed RDP failures 
  
![image](https://user-images.githubusercontent.com/112146207/232677531-d8b20f1d-f561-4105-9585-70e24baae515.png)
<details close>

---

</summary>

In this section, we will bring tenant-level logs from Azure Active Directory 
 
The most important part of this lab is to get the "Azure Active Directory Premium P2"

Go to Active Directory> Licenses > All products

Then click "add" and you should be able to see the free trial to Premium P2

After that is all set and done 

Search "Azure AD" go to "Security" then click "Identity Protection" and find "User Risk Policy"

Make sure that you have a user risk policy on 

Sign-in risk policy should also be on 
  
![image](https://user-images.githubusercontent.com/112146207/232914636-98383955-f4f8-412b-a5e4-f6a941508934.png)
  
Go to Azure AD and search for "diagnostic settings"

We will configure which logs we want to be collected 
 
![image](https://user-images.githubusercontent.com/112146207/232916264-d04e3c5f-4979-4282-855a-31a275df883e.png)
  
In Azure AD look for "users" and create a new user 

![image](https://user-images.githubusercontent.com/112146207/232917967-8c2786ca-1d8c-4afb-a201-a16d9cd01ad1.png)

Next, we will assign our dummy_user the role of Global Administrator 
  
![image](https://user-images.githubusercontent.com/112146207/232919275-093723f3-579e-4587-a580-0954f07d08a2.png)
  
Now we have to delete our dummy_user
  
![image](https://user-images.githubusercontent.com/112146207/232920249-a0fa6588-8650-48fd-b009-6a6f39b30a0f.png)
  
We will now simulate a brute force attack against AAD

Then we will observe those logs in the work analytics workspace 
  
First, get your V.S code 
  
![image](https://user-images.githubusercontent.com/112146207/233215613-0b2e01f1-1f40-4a80-8aff-4a872309ae60.png)

Run the "AAD-Brute-Force-Success-Simulator.ps1" from within your attack-VM
  
![image](https://user-images.githubusercontent.com/112146207/233221860-106b7e3f-b176-4f47-bf82-b2a0d54202df.png)
  
 We now go back to our log analytics workspace 

Click on "logs" 

We are using KQL to query logs we want to see
  
![image](https://user-images.githubusercontent.com/112146207/233224548-ad887a59-bd80-48a3-b4a3-2f0196f4b52e.png)

<details close>

In this lab, we will bring in subscription-level logging (activity log)
  
First, we will export Azure Activity Logs to the log analytics workspace 
  
Go to "Azure monitor" click "activity log" and find "export activity logs" 

From there click "Add diagnostic setting" 
  
![image](https://user-images.githubusercontent.com/112146207/233226463-261d0029-d59e-4f6e-b392-3c1e63b47e68.png)

We will now create a new resource group named "Scratch-Resource Group" and "critical infrastructure wastewater" 

![image](https://user-images.githubusercontent.com/112146207/233227240-5c494dbb-0dec-4649-9aaf-d55845edeb6f.png)

We will now delete the resource groups we just created 

The reason for this is that we want to generate logs and observe them 
  
We will produce test lab queries 

This is to better understand KQL and how to use it to filter through log activity
  
![image](https://user-images.githubusercontent.com/112146207/233230084-af667fea-5b7e-4fb4-81f2-a1d43227e4e4.png) 
<details close>

---

</summary>
  
In this lab, we will collect logs for our blob storage and our key vault

We will Configure logging for our storage account by enabling diagnostic settings for blob storage

![image](https://user-images.githubusercontent.com/112146207/233239472-a29723af-0560-45ea-86f6-b604f8475c7a.png)
  
Generate some Logs for Azure Storage (read some blobs/files)
  
![image](https://user-images.githubusercontent.com/112146207/233239985-f46c4019-6955-484d-81d3-109330c46641.png)
  
We are going to create a diagnostic setting to enable logging of the key vault
  
![image](https://user-images.githubusercontent.com/112146207/233242773-15eb31ee-af85-465c-8829-396201ab2934.png)

We will now configure logging for our key vault 
  
First, we will create a Key Vault Instance
  
![image](https://user-images.githubusercontent.com/112146207/233240688-6d9b72f0-2e52-47cd-8a62-022ed5cf76ae.png)

- Now we will add a secret to Key Vault called “Tenant-Global-Admin-Password” with a made-up password
  
![image](https://user-images.githubusercontent.com/112146207/233241999-ced3fa1c-47a4-4f10-b861-b336ea2cd6c2.png)

- Generate some Logs for Key Vault by reading this secret within the Azure Portal

> Observe the Logs (they may take a moment to appear) -  Use the KQL Query Cheat Sheet
