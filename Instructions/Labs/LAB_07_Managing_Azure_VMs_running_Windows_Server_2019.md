---
lab:
    title: 'Lab: Managing Azure VMs running Windows Server 2019'
    module: 'Module 7: Managing and maintaining Azure VMs' 
---

# Lab: Managing Azure VMs running Windows Server 2019

## Lab scenario

To address concerns regarding the outdated operational model and Information Security concerns regarding additional controls that should be applied to Azure VMs running Windows Server-based workloads, you will test a new approach to management tasks that target Windows Server 2019 running in Azure VMs.

Rather than relying on Remote Desktop sessions—which are currently used for management of the on-premises computers running the Windows OS—for this purpose you will use a range of other tools at your disposal that streamline the running of management tasks.

## Objectives

After completing this lab, you'll be able to:

- Provision Azure VMs running Windows Server 2019.
- Manage Azure VMs running Windows Server 2019 by using Windows Admin Center.
- Manage Windows Server 2019 running in Azure VMs by using PowerShell Remoting.
- Manage Windows Server 2019 running in Azure VMs by using Run Command.
- Manage Windows Server 2019 in Azure VMs by using the serial console.
- Manage Windows Server 2019 in Azure VMs by using Azure Policy Guest Configuration.
- Deprovision the Azure lab environment.

## Estimated time: 60 minutes

## Lab setup

You'll need **WS-012T00A-SEA-DC1** and **WS-012T00A-SEA-CL1** to complete this lab.

Sign in to **SEA-CL1** as **Contoso\\Administrator** by using **Pa55w.rd** as the password.

You will browse to the Azure portal from **SEA-CL1** and then use Azure VMs running Windows Server 2019 installed by using Azure PowerShell. 

## Exercise 1: Provisioning Azure VMs running Windows Server 2019

## Scenario

You need to test a number of Azure VM management scenarios. To start, you will provision Azure VMs running Windows Server 2019 by using Azure PowerShell.

The main tasks for this exercise are to complete the following preparation steps:

1. Create a resource group.
1. Upload PowerShell scripts into Cloud Shell home directory.
1. Create two Azure VMs by using Azure Cloud Shell.

### Task 1: Create a resource group

1. On **SEA-CL1**, start Microsoft Edge, navigate to the [Azure portal](https://portal.azure.com), and sign in by using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, create a resource group with the following settings: 

   *Table 1: Resource group settings*

   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you will be using in this lab.|
   |Resource group|ws2019-07-rg1|
   |Region|Use the name of an Azure region in which you can provision Azure virtual machines.|


### Task 2: Upload PowerShell scripts into Cloud Shell home directory

1. On **SEA-CL1**, in the Microsoft Edge browser displaying the Azure portal, in a new browser tab, start a PowerShell session in the [Cloud Shell](https://shell.azure.com). If prompted to authenticate, sign in by using a user account with the Owner role in the Azure subscription you are using in this lab.

    >**Note**: If this is the first time you are starting **Cloud Shell**, use the default settings to configure it. 

1. In the Cloud Shell tab, upload the following scripts from **C:\\Labfiles\\Mod07\\Scripts**: 
   
   - **Mod07Network.ps1**
   - **Mod07GW.ps1**
   - **Mod07TG.ps1**

### Task 3: Create two Azure VMs by using Azure Cloud Shell

1. On **SEA-CL1**, in the Microsoft Edge browser displaying the Cloud Shell, from the PowerShell prompt, run the following command to create virtual network resources:

   ```powershell
   ./Mod07Network.ps1
   ```

    > **Note**: Ignore warnings regarding changes to PowerShell syntax. 

1. In the Cloud Shell window, at the PowerShell prompt, run the following command to create the **Mod07Gateway** VM:

   ```powershell
   ./Mod07GW.ps1
   ```

1. When prompted, authenticate by using the following credentials: 

   *Table 2: Mod07Gateway VM local Administrator credentials*

   |Setting|Value|
   |---|---|
   |User|Student|
   |Password|Pa55w.rd1234|

   > **Note**: It might take about 3-5 minutes to create the **Mod07Gateway** VM.

1. In the Cloud Shell window, at the PowerShell prompt, run the following command to create the **Mod07Target** VM:

   ```powershell
   ./Mod07TG.ps1
   ```

1. When prompted, authenticate by using the following credentials: 

   *Table 3: Mod07Target VM local Administrator credentials*

   |Setting|Value|
   |---|---|
   |User|Student|
   |Password|Pa55w.rd1234|

   > **Note**: It might take approximately three to five minutes to create the **Mod07Target** VM.

   > **Note**: Leave the Azure Cloud Shell tab open, because you'll use it later in this lab. After 20 minutes, the Cloud Shell will automatically disconnect, but you can select the **Reconnect** button to return to the PowerShell prompt.


## Exercise 2: Managing Azure VMs running Windows Server 2019 by using Windows Admin Center

## Scenario

To manage Azure VMs in a consistent manner, you'll install Windows Admin Center into an Azure VM and use it for common management tasks on another Azure VM. 

The main tasks for this exercise are:

1. Install Microsoft Edge and Windows Admin Center on the Mod07Gateway Azure VM.
1. Add the Mod07Target VM to Windows Admin Center on Mod07Gateway VM.
1. Use Windows Admin Center to install the Web Server role on Mod07Target.

### Task 1: Install Microsoft Edge and Windows Admin Center on the Mod07Gateway Azure VM

1. On **SEA-CL1**, in the Microsoft Edge window, from the Azure portal, connect to **Mod07Gateway** via Remote Desktop. 
1. When prompted to authenticate, sign in as **Student** with the **Pa55w.rd1234** password.
1. Within the Remote Desktop session to **Mod07Gateway**, disable the **IE Enhanced Security Configuration** for Administrators. 
1. Within the Remote Desktop session to **Mod07Gateway**, use Internet Explorer to download Microsoft Edge and install it with the default settings. 
1. Within the Remote Desktop session to **Mod07Gateway**, use Microsoft Edge to download [Windows Admin Center](https://aka.ms/windows-admin-center-overview). 
1. Within the Remote Desktop session to **Mod07Gateway**, install Windows Admin Center with the default settings. 

### Task 2: Add the Mod07Target VM to Windows Admin Center on Mod07Gateway VM

1. Within the Remote Desktop session to **Mod07Gateway**, use Microsoft Edge to access the locally installed Windows Admin Center instance at `https://Mod07Gateway`.
1. From **Windows Admin Center**, add a connection to **Mod07Target**.

### Task 3: Use Windows Admin Center to install the Web Server role on Mod07Target

1. Within the Remote Desktop session to **Mod07Gateway**, in the Microsoft Edge browser, open a new tab and browse to `http://mod07target`. Note that the target page can't be reached. Keep the tab open and switch back to **Windows Admin Center**.
1. In **Windows Admin Center**, connect to **Mod07Target** and use the **Roles & features** feature to install on it the **Web Server (IIS)** server role and its services. 
1. When the installation completes, switch to the Microsoft Edge tab targeting the `http://mod07target` URL and refresh the browser page. Verify that the browser displays the **Internet Information Services Welcome** page.
1. Switch back to **Windows AdminCenter** and, from the connection to **Mod07Target**, shut down its operating system.
1. Leave the Remote Desktop connection to **Mod07Gateway** open.

## Exercise 3: Managing Windows Server 2019 running in Azure VMs by using PowerShell Remoting

## Scenario

You must verify that you can perform scripted tasks targeting Azure VMs via PowerShell Remoting. For this purpose, you'll use Azure Cloud Shell.

The main tasks for this exercise are:

1. Configure PowerShell Remoting of an Azure VM running Windows Server 2019.
1. Manage Windows Server 2019 running in an Azure VM by using PowerShell Remoting.

### Task 1: Configure PowerShell Remoting of an Azure VM running Windows Server 2019

1. On **SEA-CL1**, in the Microsoft Edge window, switch to Azure Cloud Shell. If needed, select **Reconnect**.
1. In the Cloud Shell window, at the PowerShell prompt, run the following command to start the **Mod07Target** Azure VM:

   ```powershell
   Start-AzVM -ResourceGroupName ws2019-07-rg1 -Name Mod07Target
   ```

1. After the VM successfully starts, in the Cloud Shell window, at the PowerShell prompt, run the following command to configure PowerShell Remoting on the **Mod07Target** Azure VM:

   ```powershell
   Enable-AzVMPSRemoting -Name 'Mod07Target' -ResourceGroupName 'ws2019-07-rg1' -Protocol https -OsType Windows
   ```

   > **Note**: The **Enable-AzureVMPSRemoting** cmdlet configures WinRM on the target VM and configures its Network Security Group to allow access via Windows Remote Management.


### Task 2: Manage Windows Server 2019 running in an Azure VM by using PowerShell Remoting

1. In the Cloud Shell window, at the PowerShell prompt, run the following command to list Windows services that have names that begin with **Win** installed within the operating system of the **Mod07Target** Azure VM:

   ```powershell
   Invoke-AzVMCommand -Name 'Mod07Target' -ResourceGroupName 'ws2019-07-rg1' -ScriptBlock {get-service win*} -Credential $Cred
   ```

1. When prompted, authenticate as **Student** with the **Pa55w.rd1234** password. 
1. Review the results displayed in the Cloud Shell pane and verify that they include a list of services that have names that begin with **Win**. 
1. In the Cloud Shell window, at the PowerShell prompt, run the following command to start an interactive PowerShell Remoting session within the operating system of the **Mod07Target** Azure VM:

   ```powershell
   Enter-AzVM -Name Mod07Target -ResourceGroupName ws2019-07-rg1 -Credential $Cred
   ```

1. When prompted, authenticate as **Student** with the **Pa55w.rd1234** password. 

   > **Note**: You will be presented with an interactive session prompt. This allows you to run Windows PowerShell cmdlets directly against the target VM until you exit the session.

1. At the PowerShell Remoting session, run the following command to list locally installed Windows services that have names that begin with **Win**:

   ```powershell
   Get-Service Win*
   ```

   > **Note**: The results should include the same list of services that you reviewed earlier in this task.

1. At the PowerShell Remoting session, run the following command to exit the interactive PowerShell Remoting session.

   ```powershell
   Exit
   ```

1. Keep the **Cloud Shell** tab open.


## Exercise 4: Managing Windows Server 2019 running in Azure VMs by using Run Command

## Scenario

You want to explore the functionality of the Run command in the Azure portal for running simple commands targeting the Windows Server 2019 OS running in Azure VMs.

The main tasks for this exercise are:

1. Use the **EnableRemotePS** command.
1. Use the **RunPowerShellScript** command.

### Task 1: Use the EnableRemotePS command

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, navigate to the **Mod07Target** blade. 
1. From the **Mod07Target** blade, navigate to the **Mod07Target \| Run command** blade. 
1. From the **Mod07Target \| Run command** blade, use the **EnableRemotePS** feature to enable PowerShell Remoting on the target operating system.
1. Wait until script execution completes.

### Task 2: Use the RunPowerShellScript command

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, from the **Mod07Target \| Run command** blade, use the **RunPowerShellScript** feature to run `Get-Service Win*` Windows PowerShell cmdlet. 
1. Verify that the results include the same list of services that you reviewed in the previous task.
1. Close the **Run Command Script** blade.


## Exercise 5: Managing Windows Server 2019 in Azure VMs by using the serial console

## Scenario

You must ensure that you can recover the Windows Server 2019 OS running in an Azure VM, in case you can no longer access it using either RDP, the Run command, or PowerShell Remoting. To accomplish this, you'll test the Azure VM Serial console connection.

The main tasks for this exercise are:

1. Create a storage account.
1. Configure boot diagnostics for an Azure VM.
1. Use the serial console.

### Task 1: Create a storage account

> **Note**: Storage account names must be globally unique and can contain between 3 and 24 characters, including lowercase letters and numbers, starting with a letter.

1. On **SEA-CL1**, in the Microsoft Edge browser, in the Azure portal, navigate to the **Storage accounts** blade.
1. From the **Storage accounts** blade, create a storage account with the following settings (leave others with their default values):

   *Table 4: Storage account settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|ws2019-07-rg1|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of letters and digits|
   |Location|the name of the Azure region where you created the resource group in the previous task|
   |Performance|Standard|
   |Account kind|Storage (general purpose v1)|
   |Replication|Locally redundant storage (LRS)|

   > **Note**: Wait for the Storage account to be created. This should take about two minutes.

### Task 2: Configure boot diagnostics for an Azure VM

1. On **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, navigate to the **Mod07Target** Azure VM blade.
1. From the **Mod07Target** Azure VM blade, enable its boot diagnostics with the **Enable with custom storage account** option, using the storage account you created earlier in this exercise.

   > **Note**: Managed storage account does not support the serial console functionality. 

### Task 3: Use the serial console

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, from the **Mod07Target** blade, activate the serial console.

   > **Note**: The **Mod07Target \| Serial console** blade should display the **SAC>** prompt

1. On the **Mod07Target \| Serial console** blade, at the **SAC>** prompt, run **cmd** to create a channel that contains a CMD instance.
1. On the **Mod07Target \| Serial console** blade, at the **SAC>** prompt, run **ch -si 1** to switch to the channel that's running the CMD instance.
1. On the **Mod07Target \| Serial console** blade, select the Enter key, and then sign in with the following credentials:

   *Table 5: Mod07Target VM local Administrator credentials*

   |Setting|Value|
   |---|---|
   |User|Student|
   |Domain|Mod07Target|
   |Password|Pa55w.rd1234|

1. On **Mod07Target \| Serial console** blade, at the **C:\\windows\\system32** prompt, run the following to configure operating system boot options:

   ```cmd
   bcdedit /set {bootmgr} displaybootmenu yes
   bcdedit /set {bootmgr} timeout 20
   bcdedit /set {bootmgr} bootems yes
   shutdown -r -t 0
   ```

1. On the **Mod07Target \| Serial console** blade, when the EMS window displays, select the **F8** key to display the **Advanced Boot Option** screen.
1. On the **Mod07Target \| Serial console** blade, within the serial console session, navigate the **Advanced Boot Option** screen and select the **Start Windows normally** option.  
1. On the **Mod07Target \| Serial console** blade, navigate back to the the **Mod07Target** blade.

   > **Note**: The **Advanced Boot options** screen displays the message, **Choose Advanced Options for: Windows server 2016**. Mod07Target is running Windows Server 2019. This is expected, because the serial console code has not been updated on Windows Server 2019.


## Exercise 6: Managing Windows Server 2019 in Azure VMs by using Azure Policy Guest Configuration

## Scenario

You want to evaluate the capability of configuring Windows Server 2019 running in Azure VMs by using the Guest Configuration extension.

The main tasks for this exercise are:

1. Enable the Guest Configuration resource provider.
1. Assign an Azure Policy Guest Configuration by using the Azure portal.
1. Review results of the Guest Configuration policy.

### Task 1: Enable the Guest Configuration resource provider

1. On **SEA-CL1**, in the Microsoft Edge window, switch to the tab displaying Azure Cloud Shell. If needed, select **Reconnect**.
1. In the Cloud Shell window, at the PowerShell prompt, run the following command to register the Guest Configuration resource provider:

   ```powershell
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
   ```
1. Wait until the resource provider is registered.

   > **Note**: To verify the registration status, you can re-run the **Register-AzResourceProvider** cmdlet.

### Task 2: Assign an Azure Policy Guest Configuration by using the Azure portal

1. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, navigate to the **Policy \| Definitions** blade.

1. On the **Policy \| Definitions** blade, locate the **Audit Windows VMs on which the specified services are not installed and 'Running'** inititative definition.

1. From the **Policy \| Definitions** blade, create an assignment of the **Audit Windows VMs on which the specified services are not installed and 'Running'** initiative definition with the following settings: 

   
   *Table 6: Initiative assignment settings*
   
   |Setting|Value|
   |---|---|
   |Scope|**ws2019-07-rg1** resource group|
   |Description|Auditing Windows services settings|
   |Parameters (Service names)|W3SVC|
   |Create remediation task|enabled with the default settings|
   |Managed identity location|the same Azure region you used to deploy all Azure resources in this lab|
   
   > **Note**: **W3SVC** is the name of the World Wide Web Publishing Service, which you installed on **Mod07Target** earlier in this lab.

### Task 3: Review results of the Guest Configuration policy

1. On **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, navigate back to the **Policy** blade. 

1. On the **Policy** blade, note the **Audit Windows VMs on which the specified services are not installed and 'Running'** entry and verify that its **Compliance state** is listed as **Not started** 

1. On **SEA-CL1**, in the Microsoft Edge window, switch to the tab displaying Azure Cloud Shell. If needed, select **Reconnect**.

1. In the Cloud Shell window, at the PowerShell prompt, run the following command to trigger an on-demand Azure Policy compliance scan targeting resources in the resource group **ws2019-07-rg1**:

   ```powershell
   Start-AzPolicyComplianceScan -ResourceGroupName 'ws2019-07-rg1'
   ```

   > **Note**: Wait for the compliance scan to complete. For more information regarding time it takes to complete different types of Azure Policy processing, refer to [Evaluation triggers](https://aka.ms/evaluation-triggers) and [Validation frequency](https://aka.ms/guest-configuration-validation-frequency). 

   > **Note**: Since you installed Web Server (IIS) server role on **Mod07Target**, that VM should be listed as compliant. However, since the resource group **ws2019-07-rg1** also contains the **Mod07Gateway** VM which does not include the Web Server (IIS) server role, the state of that resource and the assignment should be listed as **Non-compliant**.

1. To verify compliance status of individual resources, on **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, navigate to the **Mod07Target** blade.

1. From the **Mod07Target** blade, navigate to its **Policies** blade. 

1. Verify that the **Audit Windows VMs on which the specified services are not installed and 'Running'** policy is listed as **Compliant**.

1. To verify compliance status of individual resources, on **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, navigate to the **Mod07Gateway** blade.

1. From the **Mod07Gateway** blade, navigate to its **Policies** blade. 

1. Verify that the **Audit Windows VMs on which the specified services are not installed and 'Running'** policy is listed as **Non-compliant**.

## Exercise 7: Deprovisioning the Azure lab environment

## Scenario

To minimize Azure-related charges, you will deprovision the Azure resources provisioned throughout this lab.

The main tasks for this exercise are:

1. Remove the policy assignment.
1. Delete the ws2019-07-rg1 resource group.

### Task 1: Remove the policy assignment

1. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, navigate to the **Policy** blade. 
1. On the **Policy** blade, delete the **Audit Windows VMs on which the specified services are not installed and 'Running'** assignment.

### Task 2: Delete the ws2019-07-rg1 resource group

1. In the Azure portal, navigate to the **ws2019-07-rg1** resource group blade.
1. From the **ws2019-07-rg1** resource group blade, delete the resource group. 

## Results

After completing this lab, you will have:

- Provisioned Azure VMs running Windows Server 2019.
- Managed Azure VMs running Windows Server 2019 by using Windows Admin Center.
- Managed Windows Server 2019 running in Azure VMs by using PowerShell Remoting.
- Managed Windows Server 2019 running in Azure VMs by using Run Command.
- Managed Windows Server 2019 in Azure VMs by using the serial console.
- Managed Windows Server 2019 in Azure VMs by using Azure Policy Guest Configuration.
- Deprovisioned the Azure lab environment.
