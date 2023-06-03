# Service-Endpoints-and-Securing-Storage

<img src="https://png.pngtree.com/png-vector/20210604/ourmid/pngtree-gray-network-placeholder-png-image_3416659.jpg" height="60%" width="60%"/>

In this lesson, you will learn how to configure service endpoints and secure storage in Azure. You will perform various tasks such as creating a virtual network, configuring subnets, setting up network security groups, creating storage accounts, deploying virtual machines, and testing storage connections. This lab can be found from [Microsoft](https://microsoftlearning.github.io/AZ500-AzureSecurityTechnologies/Instructions/Labs/LAB_12_SecuringAzureStorage.html).

<details> 
  
<summary>  

### Task 1: Create a virtual network
  
</summary>  

- Sign in to the Azure portal using an account with the Owner or Contributor role in the Azure subscription.

- In the Azure portal, search for "Virtual networks" and click on it.

- On the Virtual Networks blade, click "+ Create" to create a new virtual network.

- On the Basics tab of the Create virtual network blade, specify the following settings:

| Subscription: | Select the Azure subscription you are using for this lab.|
| ---------| ------- | 
| Resource group: | Click "Create new" and enter the name "AZ500LAB12".|
| Name: | Enter "myVirtualNetwork".|
| Region: | Select "(US) South Central US".|

<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/223e7bcc-44f9-45eb-ba23-7b35cad0fc24" height="60%" width="60%" alt="Create Virtual Network"/>  
  
- On the IP addresses tab of the Create virtual network blade, set the IPv4 address space to "10.0.0.0/16".

- In the Subnet name column, click on "default" and on the Edit subnet blade, specify the following settings:

| Subnet name: | Enter "Public".|
| ------ | ----- |  
| Subnet address range: | Enter "10.0.0.0/24".|
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/80e2ed79-9e7e-4099-b90b-ce014ddcd1c8" height="90%" width="90%" alt="Create Public Subnet"/>  
  
- Click "Review + create" and then click "Create" to create the virtual network.
  
</details>  

#

<details> 
  
<summary>   

### Task 2: Add a subnet to the virtual network and configure a storage endpoint
  
</summary>   

Navigate back to the Virtual Networks blade.

Click on the "myVirtualNetwork" entry.

In the Settings section, click on "Subnets".

On the Subnets blade, click "+ Subnet" to add a new subnet.
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/a0eaca48-d7c6-45ed-af11-a79fba3cb668" height="90%" width="90%" alt="Add a Subnet"/>
  

On the Add subnet blade, specify the following settings:

| Subnet name: | Enter "Private".|
|--------|-----|  
| Subnet address range: | Enter "10.0.1.0/24".|
| Service endpoints: | Select "Microsoft.Storage".|
  
Click "Save" to add the new subnet.
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/e9316c6c-f0fc-4b80-b5a7-71a56ceb28f8" height="50%" width="50%" alt="Add a Private Subnet"/>  
  
  >**Note**: The virtual network now has two subnets: Public and Private.  
  
</details>

#

<details> 
  
<summary>   

### Task 3: Configure a network security group to restrict access to the subnet
  
  </summary>   

In the Azure portal, search for "Network security groups" and click on it.

On the Network security groups blade, click "+ Create" to create a new network security group.

On the Basics tab of the Create network security group blade, specify the following settings:

| Subscription: | Select the Azure subscription you are using for this lab.|
| --------| ------- |  
| Resource group: | Select "AZ500LAB12".|
| Name: | Enter "myNsgPrivate".|
| Region: | Select "South Central US".|
  
Click "Review + create" and then click "Create" to create the network security group.
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/117fb978-89b2-4a71-a196-d3b004fdb099" height="60%" width="60%" alt="Crteate Network Security Group"/>
  

On the myNsgPrivate blade, in the Settings section, click "Outbound security rules".

On the Outbound security rules blade, click "+ Add" to add a new outbound security rule.
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/e4df1b28-caab-4b0f-ac1f-1c8ad84409a1" height="90%" width="90%" alt="Add Outbound Security Rules"/>
  

On the Add outbound security rule blade, specify the following settings to allow outbound traffic to Azure Storage:

| Source: | Select "Service Tag"|
|----------- | ----------- |
| Source service tag: | Select "VirtualNetwork" |
| Source port ranges: | Enter "*" |
| Destination: | Select "Service Tag"|
| Destination service tag: | Select "Storage" |
| Destination port ranges: | Enter "*"|
|Protocol: | Select "Any" |
|Action: | Select "Allow" |
|Priority: | Enter "1000" |
| Name: | Enter "Allow-Storage-All|
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/2cffba2c-f283-4fb8-9ada-1c6618297148" height="40%" width="40%" alt="Create Outbound Security Rule Settings"/> 
  
On the Add outbound security rule blade, click Add to create the new outbound rule.

On the myNsgPrivate blade, in the Settings section, click Outbound security rules, and then click + Add.

On the Add outbound security rule blade, specify the following settings to explicitly deny outbound traffic to Internet (leave all other values with their default settings):

| Setting |	Value|
|-------|-------|  
| Source |	Service Tag|
| Source service tag |	VirtualNetwork||
| Source port ranges |	*|
| Destination |	Service Tag|
| Destination service tag |	Internet|
| Destination port ranges|	*|
| Protocol |	Any|
| Action |	Deny|
| Priority |	1100|
| Name |	Deny-Internet-All|
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/d52f6c89-d3c3-4035-8348-dbbde5ffdd2b" height="40%" width="40%" alt="Create Outbound Security Rule Settings (2)"/>  
  
  >**Note**: This rule overrides a default rule in all network security groups that allows outbound internet communication.

  >**Note**: In the next steps, you will create an inbound security rule that allows Remote Desktop Protocol (RDP) traffic to the subnet. The rule overrides a default security rule that denies all inbound traffic from the internet. Remote Desktop connections are allowed to the subnet so that connectivity can be tested in a later step.  
  
On the Add inbound security rule blade, click Add to create the new inbound rule.  

<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/c6f61f64-23ec-4952-be8f-da28504e4bc9" height="100%" width="100%" alt="Create Inbound Security Rule Setting"/>
  
  On the myNsgPrivate blade, in the Settings section, click Inbound security rules and then click + Add.

On the Add inbound security rule blade, specify the following settings (leave all other values with their default values):

| Setting |	Value|
|------|------|  
| Source |	Any|
| Source port ranges |	*|
| Destination |	Service Tag|
| Destination service tag	| VirtualNetwork|
| Destination port ranges |	3389|
| Protocol |	TCP|
| Action |	Allow|
| Priority |	1200|
| Name |	Allow-RDP-All|
  

  >**Note**: Now you will associate the network security group with the Private subnet.

On the Subnets blade, select + Associate and specify the following settings in the Associate subnet section and then click OK:

| Setting |	Value|
|--------|------|  
| Virtual network |	myVirtualNetwork|
| Subnet |	Private |
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/d2f38c50-04b2-4da9-be30-bcc09f79a92b" height="80%" width="80%" alt="Associate NSG to Prviate Subnet"/>
  

</details>     
  
#

<details> 
  
<summary>  
  
### Task 4: Configure a network security group to allow rdp on the public subnet
  
  </summary>   
  
In this task, you will create a network security group with one inbound security rule (RDP). You will also associate the network security group with the Public subnet. This will allow RDP access to the Public VM.

In the Azure portal, in the Search resources, services, and docs text box at the top of the Azure portal page, type Network security groups and press the Enter key.

On the Network security groups blade, click + Create.

On the Basics tab of the Create network security group blade, specify the following settings:

| Setting |	Value|
|-------|-------|  
| Subscription |	the name of the Azure subscription you are using in this lab|
| Resource group |	AZ500LAB12|
| Name |	myNsgPublic|
| Region |	South Central US|
  
Click Review + create and then click Create. 
  
  <img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/dabe05fa-5207-4f07-abac-c0cce6b31706" height="60%" width="60%" alt="Create Network Security Group Public"/>


  >**Note**: In the next steps, you will create an outbound security rule that allows communication to the Azure Storage service.

In the Azure portal, navigate back to the Network security groups blade and click the myNsgPublic entry.

On the myNsgPublic blade, in the Settings section, click Inbound security rules and then click + Add.

On the Add inbound security rule blade, specify the following settings (leave all other values with their default values):

| Setting |	Value|  
|------|------|  
| Source |	Any|
| Source port ranges |	*|
| Destination |	Service Tag|
| Destination service tag |	VirtualNetwork|
| Destination port ranges	3389|
| Protocol |	TCP|
| Action |	Allow|
| Priority |	1200|
| Name |	Allow-RDP-All|
  
On the Add inbound security rule blade, click Add to create the new inbound rule.  
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/4bd6da8e-76a5-4a6d-b7da-c8fbf4210adc" height="90%" width="90%" alt="Create Inbound Security Rule (public)"/>  

 >**Note**: Now you will associate the network security group with the Public subnet.

On the Subnets blade, select + Associate and specify the following settings in the Associate subnet section and then click OK:

|Setting	| Value|
|------|-------|  
|Virtual network	| myVirtualNetwork|
|Subnet |	Public|
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/4035cbfd-ebe3-4939-b42b-d70dc029e074" height="90%" width="90%" alt="Associate NSG to Public Subnet"/>
  

</details> 

#

<details> 
  
<summary> 
  
### Task 5: Create a storage account with a file share
  
</summary>   
  
In this task, you will create a storage account with a file share and obtain the storage account key.

In the Azure portal, in the Search resources, services, and docs text box at the top of the Azure portal page, type Storage accounts and press the Enter key.

On the Storage accounts blade, click + Create.

On the Basics tab of the Create storage account blade, specify the following settings (leave others with their default values):

| Setting |	Value|
|------|------|  
| Subscription |	the name of the Azure subscription you are using in this lab|
| Resource group |	AZ500LAB12|
| Storage account name |	any globally unique name between 3 and 24 in length consisting of letters and digits|
| Location |	(US) EastUS|
| Performance |	Standard (general-purpose v2 account)|
| Redundency |	Locally redundant storage (LRS)|
  
On the Basics tab of the Create storage account blade, click Review + Create, wait for the validation process to complete, and click Create.
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/1454986c-12e0-4436-9e28-258e214a650e" height="60%" width="60%" alt="Create Storage Account"/>
  

  >**Note**: Wait for the Storage account to be created. This should take about 2 minutes.

In the Azure portal, in the Search resources, services, and docs text box at the top of the Azure portal page, type Resource groups and press the Enter key.

On the Resource groups blade, in the list of resource group, click the AZ500LAB12 entry.

On the AZ500LAB12 resource group blade, in the list of resources, click the entry representing the newly created storage account.

On the storage account Overview blade, click File Shares under the Data storage tab, and then click + File Share.

On the New file share blade, specify the following settings:

Setting	Value
Name	my-file-share
On the New file share blade, click Create.
  
<img src="https://github.com/0xbythesecond/Service-Endpoints-and-Securing-Storage/assets/23303634/81d89556-648f-4afc-9486-4ea90c179fa6" height="80%" width="80%" alt="Create a File Share"/>
  

  >**Note**: Now, retrieve and record the PowerShell script that creates a drive mapping to the Azure file share.

On the storage account blade, in the list of file shares, click my-file-share.

On the my-file-share blade, click Connect.

On the Connect blade, on the Windows tab, copy the PowerShell script that creates a Z drive mapping to the file share.

  >**Note**: Record this script. You will need this in a later in this lab in order to map the file share from the Azure virtual machine on the Private subnet.

Navigate back to the storage account blade, then in the Security + networking section, click Networking.

Under Firewalls and virtual networks blade, select the Enabled from selected virtual networks and IP addresses option and click the + Add existing virtual network link.

On the Add networks blade, specify the following settings:

| Setting |	Value |
|------|------|
| Subscription |	the name of the Azure subscription you are using in this lab|
| Virtual networks |	myVirtualNetwork| 
|Subnets |	Private|
  
On the Add networks blade, click Add.

Back on the storage account blade, click Save.

  >**Note**: At this point in the lab you have configured a virtual network, a network security group, and a storage account with a file share.

</details> 

#

<details>
  
<summary>   
  
### Task 6: Deploy virtual machines into the designated subnets
  
  </summary>   
  
In this task, you will create two virtual machines one in the Private subnet and one in the Public subnet.

  >**Note**: The first virtual machine will be connected to the Private subnet.

In the Azure portal, in the Search resources, services, and docs text box at the top of the Azure portal page, type Virtual machines and press the Enter key.

On the Virtual machines blade, click + Create and, in the dropdown list, click + Azure Virtual machine

On the Basics tab of the Create a virtual machine blade, specify the following settings (leave others with their default values):

| Setting |	Value| 
| ------|------|  
| Subscription |	the name of the Azure subscription you will be using in this lab
| Resource group |	AZ500LAB12|
| Virtual machine name |	myVmPrivate|
| Region |	(US)East US|
| Image	Windows Server 2022 Datacenter: | Azure Edition - Gen 2|
| Username |	localadmin|
| Password |	Please use your personal password created in Lab 04 > Exercise 1 > Task 1 > Step 9.
| Public inbound ports |	None| 
  
Already have a Windows Server license	Not selected

  >**Note**: For public inbound ports, we will rely on the precreated NSG.

Click Next: Disks > and, on the Disks tab of the Create a virtual machine blade, set the OS disk type to Standard HDD and click Next: Networking >.

Click Next: Networking >, on the Networking tab of the Create a virtual machine blade, specify the following settings (leave others with their default values):

| Setting |	Value|
|-------|------|  
| Virtual network |	myVirtualNetwork|
| Subnet |	Private (10.0.1.0/24)|
| Public IP |	(new)myVmPrivate-ip|
| NIC network security group |	None|
Click Next: Management >, on the Management tab of the Create a virtual machine blade, accept the default settings and click Review + create.

On the Review + create blade, ensure that validation was successful and click Create.

Note: The second virtual machine will be connected to the Public subnet.

On the Virtual machines blade, click + Add and, in the dropdown list, click + Azure Virtual machine.

On the Basics tab of the Create a virtual machine blade, specify the following settings (leave others with their default values):

| Setting |	Value|
Subscription	the name of the Azure subscription you will be using in this lab
Resource group	AZ500LAB12
Virtual machine name	myVmPublic
Region	(US)East US
Image	Windows Server 2022 Datacenter: Azure Edition - Gen 2
Username	localadmin
Password	Please use your personal password created in Lab 04 > Exercise 1 > Task 1 > Step 9.
Public inbound ports	None
Already have a Windows Server license	Not selected
Note: For public inbound ports, we will rely on the precreated NSG.

Click Next: Disks > and, on the Disks tab of the Create a virtual machine blade, set the OS disk type to Standard HDD and click Next: Networking >.

Click Next: Networking >, on the Networking tab of the Create a virtual machine blade, specify the following settings (leave others with their default values):

| Setting |	Value|
| --------|-------| 
| Virtual | network	myVirtualNetwork|
| Subnet |	Public (10.0.0.0/24)|
| Public IP |	(new)myVmPublic-ip|
NIC network security group |	None|
Click Next: Management >, on the Management tab of the Create a virtual machine blade, accept the default settings and click Review + create.

On the Review + create blade, ensure that validation was successful and click Create.

  >**Note**: You can continue to the next task once the deployment of the myVMPrivate Azure VM is completed.
  
</details> 

#

<details>

<summary> 

### Task 7: Test the storage connection from the private subnet to confirm that access is allowed
  
</summary> 

In this task, you will connect to the myVMPrivate virtual machine via Remote Desktop and map a drive to the file share.

Navigate back to the Virtual machines blade.

On the Virtual machines blade, click the myVMPrivate entry.

On the myVMPrivate blade, click Connect and, in the drop down menu, click RDP.

Click Download RDP File and use it to connect to the myVMPrivate Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

| Setting |	Value|
|------|------|
| User name |	localadmin|
| Password |	Please use your personal password created in Lab 04 > Exercise 1 > Task 1 > Step 9.|

  >**Note**: Wait for the Remote Desktop session to open and Server Manager to load.

  >**Note**: You will now map drive Z to an Azure File share within the Remote Desktop session to a Windows Server 2022 computer

Within the Remote Desktop session to myVMPrivate, click Start and then click Windows PowerShell ISE.

Within the Windows PowerShell ISE window, open the Script pane, then paste and run the PowerShell script that you recorded earlier in this lab. The script has the following format:

```powershell
 $connectTestResult = Test-NetConnection -ComputerName <storage_account_name>.file.core.windows.net -Port 445
 if ($connectTestResult.TcpTestSucceeded) {
    # Save the password so the drive will persist on reboot
    cmd.exe /C "cmdkey /add:`"<storage_account_name>.file.core.windows.net`" /user:`"localhost\<storage_account_name>`"  /pass:`"<storage_account_key>`""
    # Mount the drive
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage_account_name>.file.core.windows.net\my-file-share" -Persist
 } else {
    Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
 }
 ```
 
  >**Note**: The <storage_account_name> placeholder represents the name of the storage account hosting the file share and <storage_account_key> one its primary key

Start File Explorer and verify that the Z: drive mapping has been successfully created.

Next, from the console pane of the Windows PowerShell ISE console, run the following to verify that the virtual machine has no outbound connectivity to the internet:

Code
 Test-NetConnection -ComputerName www.bing.com -Port 80
Note: The test will fail because the network security group associated with the Private subnet does not allow outbound access to the internet.

Terminate the Remote Desktop session to the myVMPrivate Azure VM.

  >**Note**: At this point, you have confirmed that the virtual machine in the Private subnet can access the storage account.

Task 8: Test the storage connection from the public subnet to confirm that access is denied
Navigate back to the Virtual machines blade.

On the Virtual machines blade, click the myVMPublic entry.

On the myVMPublic blade, click Connect and, in the drop down menu, click RDP.

Click Download RDP File and use it to connect to the myVMPublic Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

|Setting	| Value|
|-------|------|
| User name |	localadmin|
| Password	| Please use your personal password created in Lab 04 > Exercise 1 > Task 1 > Step 9.|

  >**Note**: Wait for the Remote Desktop session to open and Server Manager to load.

  >**Note**: You will now map drive Z to an Azure File share within the Remote Desktop session to a Windows Server 2022 computer

Within the Remote Desktop session to myVMPublic, click Start and then click Windows PowerShell ISE.

Within the Windows PowerShell ISE window, open the Script pane, then paste and run the same PowerShell script that you ran within the Remote Desktop session to the myVMPrivate Azure VM.

  >**Note**: This time, you will receive the New-PSDrive : Access is denied error.

  >**Note**: Access is denied because the myVmPublic virtual machine is deployed in the Public subnet. The Public subnet does not have a service endpoint enabled for the Azure Storage. The storage account only allows network access from the Private subnet.

Next, from the console pane of the Windows PowerShell ISE console, run the following to verify that the virtual machine has outbound connectivity to the internet:

```powershell
 Test-NetConnection -ComputerName www.bing.com -Port 80
``` 
 
  >**Note**: The test will succeed because there is no outbound security rule to deny internet on the Public subnet.

Terminate the Remote Desktop session to the myVMPublic Azure VM.

  >**Note**: At this point, you have confirmed that the virtual machine in the Public subnet cannot access the storage account, but has access to the internet.

Clean up resources

Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs.

Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select PowerShell and Create storage.

Ensure PowerShell is selected in the drop-down menu in the upper-left corner of the Cloud Shell pane.

In the PowerShell session within the Cloud Shell pane, run the following to remove the resource group you created in this lab:

```powershell
 Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
```

Close the Cloud Shell pane.
  </details>
