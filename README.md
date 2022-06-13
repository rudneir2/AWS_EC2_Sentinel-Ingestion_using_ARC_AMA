# AWS_EC2_Sentinel-Ingestion_using_ARC_AMA
Practical guide to ingest logs from AWS EC2 VMs into Sentinel by using ARC agent and Azure Monitoring Agent (AMA)

## Quick Intro

You can ingest logs from VMs that runs on Azure, on-premises and from other cloud providers, including AWS. 
To ingest logs from AWS EC2 (VMs) you will have to use Azure ARC agent to make your EC2 part of Azure. That really means those EC2 will get a resource ID on AZure as they were part of Azure.

After you install the ARC agent, then you will install the Log Analytics Agent monitoring.
There are two agents available at this time (June 2022). The legacy one, also called MMA, and the newest one, called AMA (Azure Monitoring Agent. 
This practical guide will work based on the new one, AMA.

This is a simple architecture so that you may understand how that works. Below you will find a step by step, that was tested and validated on June 2022 by me.
If you want to get in touch about it, you may send me an email on ruolivei@microsoft.com.

## Architecture diagram

![image](https://user-images.githubusercontent.com/97529152/173387015-d39e7aeb-44a1-46cd-be14-38fd7e7427ba.png)


## Step by step

### 1. start with ARC Server page on Azure Portal. 

a. Search on the main search box for "Azure ARC"

b. in the left menu click on "Servers", then click "Add"

c. download one of the script available for:
"add a single server"
or
" add multiple server"

**NOTE:**
The only difference is that on the first one, as part of the ARC agent deployment, you will have to authenticate against Azure.

For the multiple servers, you will create a service principal (easy, the wizard will help you).
Take note of the clientID and clientSecret.

Then, use those information on the script for multiple servers, save the script.

d. run the script on the VM you want to install ARC agent.

NOTE:
Run Powershell as Administrator.
In a few minutes, your VM will be enrolled on Azure.

### 2. go to Sentinel, then 

a. click on left menu "Data Connectors" and search for "Windows Security Events via AMA", and then click on "open connector page"

b. on the right, click on "+create data collection rule". Fill out the fields in "Basic" tab

c. under tab "Resources", click "Add resource" and search for the resource group that you use to add ARC servers. You must see the new ARC server you just added

d. select all of them you want it, go to the next tab "Collect" and select "All security events", then click on "review and create"


### 3. When you finish the procedure above, the AMA (Azure Monitoring Agent) will be installed for you, and it will start sending logs to Log Analytics workspace (which Sentinel uses as log repository).

**NOTE: **
at this time, Microsoft has 2 types of agents for monitoring. AMA and MMA (sorry about the confusion : ) ... MMA is the legacy one and it will be deprecated in July 2024. 

### 4. to test what you did all above, go to Sentinel main page, click on "logs" in the left menu and run this query:
```
  search "EC2"
  | summarize by $table
```
check on the results, in which table you are receiving logs from your new ARC server.
after a couple of minutes, you should see the table SecurityEvent populated. Check with that query:
```
  SecurityEvent
  | where Computer contains "EC2"
  | summarize count() by EventID
```
