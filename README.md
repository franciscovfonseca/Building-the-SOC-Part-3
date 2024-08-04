<br>

<h1 align="center">Stage II - Building the SOC - Part 3</h1>

<br>

<h2 align="center"> Configure Log Collection for Virtual Machines</h2>

![Banner](https://github.com/user-attachments/assets/aed12083-4d26-499f-80b5-8a9e1db584f7)
<br />
<br />

<details close> 
<summary> <h2>1️⃣ Create a Storage Account</h2> </summary>
<br>
  
We'll first create an **Azure Storage Account** ➜ where we'll later place the **NSG Flow Logs**.

<br>

>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> The Storage Account Name must be globally unique.
> 
>   </details>

<br>

![azure portal](https://github.com/user-attachments/assets/6212a9be-938b-4627-a5f9-c5a50891b90f)

<br>

⚠️ Make sure the Storage Account is in the **Same Region** as the Target Virtual Machines!

<br>

![azure portal](https://github.com/user-attachments/assets/e49a299a-d421-4b34-908f-871e927a5197)

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>2️⃣ Enable NSG Flow Logs for Target VMs</h2> </summary>
<br>

Go to **"Network Security Groups"** ➜ pick one of the **NSGs** attached to one of the Target VMs ➜ I picked our ```windows-vm-nsg```

Go to the **"NSG flow logs"** blade ➜ and click on the **"Create flow log"** button:

<br>

![azure portal](https://github.com/user-attachments/assets/19b136ee-45f4-432a-bb60-76924f070822)

<br>

Click on ➕ **Select resource**

<br>

![azure portal](https://github.com/user-attachments/assets/28cd6acc-58c7-40b1-87b7-ba9a40f7e3eb)

<br>

Select both ☑️ ```windows-vm-nsg``` & ☑️ ```linux-vm-nsg``` ➜ **Confirm selection**

<br>

![azure portal](https://github.com/user-attachments/assets/b8e0ba6b-a7d1-4b8d-addc-be3ac048ddf8)

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Create a Data Collection Rule for Target VMs</h2> </summary>
<br>

First ➜ make sure the **Target VMs are Running**.

<br>

>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> The Microsoft Defender will:
> 
> - Either **Automatically Install the Agent to the VMs** once they are running
> 
> - Or if not ➜ you can **Manually Install it** later.
> 
>   </details>

<br>

Next ➜ go to our Log Analytics Workspacethe

Inside the **"Agents"** blade ➜ click on the **"Data Collection Rules"** button

<br>

![azure portal](https://github.com/user-attachments/assets/bed116c1-ad5a-4049-a517-9f67d557282c)

<br>

We'll then click on **"Create data collection rule"**:

<br>

![azure portal](https://github.com/user-attachments/assets/e5499a64-ff0f-4bf1-96e3-1f8e61b9d818)

<br>

We'll create the new **Data Collection Rule** with the following details:
- **Rule Name**: ```dcr-all-vms``` ➜ 💡 this stands for 1 single DCR that will apply to all of our VMs
- **Resource group**: ```RG-Cyber-Lab```
- **Region**: ```East US``` ➜ ⚠️ make sure you put it in the **Same Region as you Target VMs**, otherwise it won't work!
- **Platform Type**: ⦿ **All**

Then click **"Next"** to reach the **Resources** tab:

<br>

![azure portal](https://github.com/user-attachments/assets/c12cd00c-ae7a-48c7-8475-b584482660e5)

<br>

For the Resources we're going to ➕ **Add Resources**:

<br>

![azure portal](https://github.com/user-attachments/assets/9c21df37-facf-4a68-8a8b-3a43c80786c4)

<br>

Now expand the **> RG-Cyber Lab** Resource Group ➜ and select ☑️ for both of our VMs ➜ click **"Apply"**:

<br>

![azure portal](https://github.com/user-attachments/assets/cb18416b-b03a-4c40-873a-d179b31bbbd5)

<br>

Click **"Next: Collect and deliver >"**

<br>

![azure portal]https://github.com/user-attachments/assets/bc591dd2-15d7-4aa7-a807-c16f48df7c34)

<br>

💡 This is where we'll specify which Logs from within the VMs we're going to collect.

So we'll click on the ➕ **Add data source** button:

We'll do the **"Linux Syslog"** first.

In this Lab, the only Logs we're going to Collect from the Linux VM are the ```LOG_AUTH``` Logs.

<br>

>   <details close> 
>   
> **<summary> 💡 </summary>**
>   
> The AUTH LOGS are the Logs we inspected earlier ➜ where we saw all the **SSH Failure Attempts**.
> 
> For the data source settings, when selecting the **"Minimum log level"** ➜ this signifies the level of logging we want to collect:
> 
> DEBUG essentially means "collect everything", and then in kind of scales down towards EMERG which means "only collect critical logs".
> 
>   </details>

<br>

So for **LOG_AUTH** ➜ leave the **"Minimum log level"** at ```LOG_DEBUG``` ➜ meaning we'll collect all the Auth Logs.

And then we'll select ```LOG_DEBUG``` for the rest of the Log Types.

After setting that up ➜ click on the **"Next : Destination"** button:

<br>

![azure portal](https://github.com/user-attachments/assets/bf816bca-c0ba-4c7b-aaeb-877e9a86dbfb)

<br>

For **Destination** ➜ make sure you're sending the data to your actual **LAW**, not the random one that was created! ⚠️

Click **"Add data source"**:

<br>

![azure portal](https://github.com/user-attachments/assets/4be175b1-e230-4bdb-be4b-ef90c20ae886)

<br>

Now click on the ➕ **Add data source** button again:

<br>

![azure portal](https://github.com/user-attachments/assets/f978b2f3-1bab-4f43-97f0-94755725ae29)

<br>

The next one is going to be **"Windows Event Logs"**.

<br>

>   <details close> 
>   
> **<summary> 💡 </summary>**
>   
> This might be hard to remember, but back when we were configuring SQL logging for the SQL Server Database ➜  the SQL Logs appeared on the Application Event Log ➜ under Information
> 
> And then for Security ➜  Audit Success & Audit Failure ➜  this is when someone tries to Remote Desktop into our Windows VM or tries to Map a File Share.
> 
>   </details>

<br>

We're going to select the ☑️ **Information** Logs from **"Application"** type.

And also select the ☑️ **Audit Success** & ☑️ **Audit Failure** Logs from the **"Security"** type.

<br>

![azure portal](https://github.com/user-attachments/assets/a0db1674-cb0a-4314-b8e7-0d35a7d88d4b)

<br>

<h2></h2>

<br>

<br>

>   <details close> 
>   
> **<summary> 💡 Summary</summary>**
>   
> Next we're going to add **Special Data Sources** to our **Data Collection Rule**.
>   
> It will Log whenever somebody messes with the Windows Firewall ➜ like if they Turn Off the Firewall for example.
> 
> And also when Malware is discovered in the Virtual Machines ➜ it will create and pull those Logs out as well.
> 
>   </details>

<br>

We'll go back to our **Log Analytics Wokspace** ➜ click on the **"Agents"** blade ➜ and then on the **"Data Collection Rules"** Button.

Click on the **Data Collection Rule** that we just created ➜ ```dcr-all-vms```

Then we'll go to the **"Data Sources"** blade ➜ and click on the **"Windows Event Logs"** Data Source.

This time instead of **"Basic"** we'll go to **"Custom"**.

<br>

>   <details close> 
>   
> **<summary> 📝 Explanation</summary>**
>   
> We can see in the image below the XPath queries that we previously selected ➜ under **Event Logs**.
>   
> Think of an XPath query as Microsoft's "convention" for specifying which Logs (Application & Security in this case) and which "Sub-Logs" inside of those two we want to capture.
> 
> So in order for us to Collect Logs from the Firewall, as well as the actual Defender Anti-Malware on the Virtual Machines ➜ we have to use this XPath syntax convention to specify which Logs to capture.
> 
>   </details>

<br>

![azure portal](https://github.com/user-attachments/assets/6d1ecad8-c69e-459c-8529-e54560f81b31)

<br>

We want to configure our **Data Collection Rule** so that:

<br>

❶ If Malware is discovered ➜ a Log is created and it's forwarded into our **LAW**.

<br>

Copy the following **Windows Defender Malware Detection XPath Query**:

<br>

```commandline
Microsoft-Windows-Windows Defender/Operational!*[System[(EventID=1116 or EventID=1117)]]
```
<br>

And now add it to the **Add Data Source Section**.

<br>

❷ Also if the Firewall is disabled or messed with ➜ we want the **Firewall Logs** to be forwarded to our **LAW** as well.

<br>

Copy the following **Windows Firewall Tampering Detection XPath Query**:

<br>

```commandline
Microsoft-Windows-Windows Firewall With Advanced Security/Firewall!*[System[(EventID=2003)]]
```
<br>

Again ➜ we'll add it to the **Add data source** section.

<br>

![azure portal](https://github.com/user-attachments/assets/4dfb39cd-d281-4867-bb2b-15ab345894c5)

<br>

Click **"Save"** and we've successfully configured our Data Collection Rule with "Special Forwarding" ✅

<br>

<h2></h2>

<br>

<br>

This is all we need for the **Windows Event Logs** ➜ so then we'll click on **"Next : Destination"**:

Once again ➜ for **Destination** ➜ we need to make sure we put our actual LAW ```LAW-Cyber-Lab-01```.

Then Add the data source:

We can now **"Review + create"** to Create the Data Collection Rule:

<br>

![azure portal](https://github.com/user-attachments/assets/cb1d6ac5-c9d7-4f12-983b-946c8fd0040f)

<br>

✅ Data Collection Rule successfully Configured and Deployed

<br>

![azure portal](https://github.com/user-attachments/assets/e2a687cd-389a-45de-92c8-8160d671fa92)

<br>

If you check back to our Log Analytics Workspace ➜ go to the **"Agents"** blade:

✅ The Agents should have already been Deployed and Successfully Installed on the Target Virtual Machines

<br>

### Windows VM: 

<br>

![azure portal](https://github.com/user-attachments/assets/c25649ea-78a9-4f1a-803c-8e146dcbbb63)

<br>

### Linux VM: 

<br>

![azure portal](https://github.com/user-attachments/assets/6b35ca45-848a-4e36-8a25-e3d9951c8d69)

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>4️⃣ Query the Log Analytics Workspace for our VM Logs & NSG Logs</h2> </summary>
<br>

Ideally at this point ➜ you would start Querying Log Analytics Workspace.

We'll Query for Logs from the VMs and their respective NSGs ➜ to make sure the Logs are coming in:

- ```Syslog``` (Linux)

- ```SecurityEvent``` (Windows)

- ```AzureNetworkAnalytics_C``` (NSGs)

<br>

### Syslog:

![azure portal](https://github.com/user-attachments/assets/65fc3eb7-a0ba-4ffb-bf8b-c8622ddb4730)

<br>

### SecurityEvent:

![azure portal](https://github.com/user-attachments/assets/140274a9-ad78-47e0-bb74-2b00b7f3ba00)

<br>

### AzureNetworkAnalytics_C:

![azure portal](https://github.com/user-attachments/assets/98d47940-5473-4e0a-a7ca-07db5c62d9a9)

<br>

  </details>

<h2></h2>

<br>

<br>

<br>

<br>

<br>

<br>

<br>
  
<br>
