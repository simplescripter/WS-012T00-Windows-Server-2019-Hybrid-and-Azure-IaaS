---
lab:
    title: 'Lab: Deploying and configuring Windows Server 2019 on Azure VMs'
    module: 'Module 6: Deploying and Configuring Azure VMs'
---

# Lab: Deploying and configuring Windows Server 2019 on Azure VMs

## Scenario

You need to address concerns regarding your current infrastructure. You have an outdated operational model, a limited use of automation, and Information Security team concerns regarding additional controls that should be applied to Azure VMs running Windows Server-based workloads. You have decided to develop and implement an automated deployment and configuration process for Azure VMs running Windows Server 2019.

The process will involve Azure Resource Manager (ARM) templates and OS configuration through Azure VM extensions. It will also incorporate additional security protection mechanisms beyond those already applied to on-premises systems, such as application whitelisting through AppLocker, file integrity checks, and adaptive network/DDoS protection. You will also leverage JIT functionality to restrict administrative access to Azure VMs to public IP address ranges associated with the London headquarters.

Your goal is to deploy and configure Azure VMs running Windows Server 2019 in the manner that satisfies manageability and security requirements.

## Objectives

After completing this lab, you'll be able to:

- Author ARM templates for an Azure VM deployment.
- Modify ARM templates to include VM extension-based configuration.
- Deploy Azure VMs running Windows Server 2019 by using ARM templates.
- Configure administrative access to Azure VMs running Windows Server 2019.
- Configure Windows Server 2019 security in Azure VMs.
- Deprovision the Azure environment.

## Estimated time: 90 minutes

## Lab setup

Lab virtual machines: **SEA-CL1** and **SEA-DC1**

User name: **CONTOSO\\Mike**

Password: **Pa55w.rd1234**

For this lab, you'll use the available VM environment and an Azure subscription. Before you begin the lab, ensure that you have an Azure subscription and a user account with the Owner or Contributor role in that subscription.

## Exercise 1: Authoring ARM templates for Azure VM deployment

### Scenario

To streamline Azure-based operations, you decide to develop and implement an automated deployment and configuration process for Windows Server 2019 to Azure VMs. Your deployments need to comply with the Information Security team's requirements and adhere to the Contoso, Ltd.'s intended target operational model, including high availability.

The main tasks for this exercise are as follows:

1. Enable the Standard tier of Security Center.
1. Generate an ARM template and parameters files by using the Azure portal.
1. Download the ARM template and parameters files from the Azure portal.

### Task 1: Enable the Standard tier of Security Center

In this task, you will enable the Standard tier of Azure Security Center.

>**Note**: Skip this task and proceed directly to the next one if you have already upgraded Security Center in your Azure subscription to the Standard tier.

1. From  **SEA-CL1**, start Microsoft Edge, and then navigate to the [Azure portal](https://portal.azure.com). When prompted, sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, navigate to the **Security Center** blade.
1. From the **Security Center \| Getting started** blade, upgrade Security Center to the Standard tier and select the option to automatically install the Security Center agents on all newly provisioned Azure VMs.

### Task 2: Generate an ARM template and parameters files by using the Azure portal

1. From the Azure portal, go through the process of creating a new Azure VM using the following settings and leaving all other settings with their default values, but do not deploy it:

   *Table 1: Azure VM settings*

   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you will be using in this lab.|
   |Resource group|The name of a new resource group ws2019-06-rg1|
   |Virtual machine name|ws2019-06-vm0|
   |Region|Use the name of an Azure region in which you can provision Azure VMs.|
   |Availability options|No infrastructure redundancy required|
   |Image|Windows Server 2019 Datacenter-Gen1|
   |Azure Spot instance|No|
   |Size|Standard D2s v3|
   |Username|Mike|
   |Password|Pa55w.rd1234|
   |Public inbound ports|None|
   |Already have a Windows Server license|No|
   |OS disk type|Standard HDD|
   |Name|ws2019-06-vnet|
   |Address range|10.60.0.0/20|
   |Subnet name|subnet0|
   |Subnet range|10.60.0.0/24|
   |Public IP|None|
   |NIC network security group|None|
   |Accelerated networking|Off|
   |Place this virtual machine behind an existing load balancing solution?|No|
   |Boot diagnostics|On|
   |Diagnostics storage account|Use the default value.|

1. When you reach the **Review + Create** tab of the **Create a virtual machine** blade, proceed to task 2.

### Task 3: Download the ARM template and parameters files from the Azure portal

1. From the **Review + Create** tab of the **Create a virtual machine** blade, download the template for automation and copy it to the **C:\\Labfiles\\Mod06** folder on the lab VM.

1. In the Azure portal, close the **Create a virtual machine** blade.

## Exercise 2: Modifying ARM templates to include VM extension-based configuration

### Scenario

In addition to automated Azure resources deployments, you also want to ensure that you can automatically configure Windows Server 2019 OS's running in Azure VMs. To accomplish this, you want to test the use of Azure Custom Script Extension.

The main tasks for this exercise are as follows:

1. Review the ARM template and parameters files for Azure VM deployment.
1. Add an Azure VM extension section to the existing template.

### Task 1: Review the ARM template and parameters files for Azure VM deployment

1. Extract the contents of the downloaded archive into the **C:\\Labfiles\\Mod06** folder.

1. Open the **template.json** file in Notepad and review the contents. Keep the Notepad window open.

1. Open the **C:\\Labfiles\\Mod06\parameters.json** file in Notepad, review it, and close the Notepad window.

### Task 2: Add an Azure VM extension section to the existing template

1. On the lab VM, in the Notepad window displaying the contents of the **template.json** file, insert the following code directly underneath the `    "resources": [` line):

   ```json
        {
           "type": "Microsoft.Compute/virtualMachines/extensions",
           "name": "[concat(parameters('virtualMachineName'), '/customScriptExtension')]",
           "apiVersion": "2018-06-01",
           "location": "[resourceGroup().location]",
           "dependsOn": [
               "[concat('Microsoft.Compute/virtualMachines/', parameters('virtualMachineName'))]"
           ],
           "properties": {
               "publisher": "Microsoft.Compute",
               "type": "CustomScriptExtension",
               "typeHandlerVersion": "1.7",
               "autoUpgradeMinorVersion": true,
               "settings": {
                   "commandToExecute": "powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)"
              }
           }
        },
   ```

1. Save the change and close the file.

## Exercise 3: Deploying Azure VMs running Windows Server 2019 by using ARM templates

### Scenario

With the ARM templates configured, you will verify their functionality by performing a deployment into your proof-of-concept Azure subscription.

The main tasks for this exercise are as follows:

1. Deploy an Azure VM by using an ARM template.
1. Review results of the Azure VM deployment.

### Task 1: Deploy an Azure VM by using an ARM template

1. In the Azure portal, navigate to the **Custom deployment** blade and then select the option to **Build your own template in the editor**.
1. Load the template file and the parameter file into the **Custom deployment** blade.
1. Deploy the template with the following settings, leaving all other settings with their default values:

   *Table 2: Custom template deployment settings*

   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you are using in this lab.|
   |Resource group|The name of a new resource group ws2019-06-rg1|
   |Region|Use the name of the Azure region into which you can provision Azure VMs.|
   |Admin Password|Pa55w.rd1234|

1. Verify that the deployment completed successfully.

> **Note**: The deployment might take about 10 minutes.

### Task 2: Review results of the Azure VM deployment

1. In the Azure portal, navigate to the **ws2019-06-rg1** resource group blade and review the list of its resources, particularly the Azure VM **ws2019-06-vm0**.
1. Navigate to the **ws2019-06-vm0** Azure VM blade and verify that the **customScriptExtension** has been provisioned successfully.
1. Navigate back to the **ws2019-06-rg1** resource group blade, review its deployments, and review the **Microsoft.Template** that was used to deploy it to confirm that it matches the template you used for deployment.

## Exercise 4: Configuring administrative access to Azure VMs running Windows Server 2019

### Scenario

With the Azure VMs running Windows Server 2019 in place, you want to test the ability to manage them remotely from your on-premises administrative workstation.

The main tasks for this exercise are as follows:

1. Verify the Azure Security Center Standard tier.
1. Review Just in time VM access settings.

### Task 1: Verify the Azure Security Center Standard tier

1. In the Azure portal, navigate to the **Security Center** blade.
1. Verify that the Standard tier for the **Security Center** is enabled.

### Task 2: Review the Just in time access settings

1. In the **Security Center** blade, navigate to the **Just in time VM access settings**.
1. Review the **Unsupported** tab and verify that an entry representing the **ws2019-06-vm0** Azure VM appears on that tab.

## Exercise 5: Configuring Windows Server 2019 security in Azure VMs

### Scenario

With the Azure VMs running Windows Server 2019 in place, you want to test the ability to manage them remotely from your on-premises administrative workstation.

The main tasks for this exercise are as follows:

1. Create and configure an NSG.
1. Configure inbound HTTP access to an Azure VM.
1. Configure inbound RDP access to the Azure VM.
1. Connect to the Azure VM via JIT VM access.

### Task 1: Create and configure an NSG

1. In the Azure portal, create an NSG with the following settings, leaving all other settings with their default values:

   *Table 3: Network security group settings*

   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you are using in this lab.|
   |Name|ws2019-06-vm0-nsg1|
   |Resource group|ws2019-06-rg1|
   |Region|Use the name of the Azure region into which you provisioned the Azure VM ws2019-06-vm0.|

1. Add an inbound security rule to the newly created network security group with the following settings, leaving all other settings with their default values, and then select **Add**:

   *Table 4: Network security group rule settings*

   |Setting|Value|
   |---|---|
   |Source|Any|
   |Source port ranges|*|
   |Destination|Any|
   |Destination port ranges|80|
   |Protocol|TCP|
   |Action|Allow|
   |Priority|300|
   |Name|AllowHTTPInBound|

### Task 2: Configure inbound HTTP access to an Azure VM

1. In the Azure portal, navigate to the blade of the network interface attached to the **ws2019-06-vm0** Azure VM, and associate it with the network security group you created in the previous task.
1. In the Azure portal, navigate to the IP configuration of the network interface attached to the **ws2019-06-vm0** Azure VM, and associate it with a new, public IP address with the following settings, leaving all others with their default values:

   *Table 5: Public IP address settings*

   |Setting|Value|
   |---|---|
   |Name|ws2019-06-vm0-pip1|
   |SKU|Standard|

1. From the lab VM, open a browser tab, navigate to the newly created public IP address, and verify that the page displays the message, **Hello World from ws2019-06-vm0**.
1. From the lab VM, attempt to establish a Remote Desktop connection to the same IP address and verify that the connection attempt fails.

> **Note**: This is expected behavior because the Azure VM is currently not accessible from the internet via TCP port 3389, only via TCP port 80.

### Task 3: Trigger re-evaluation of the JIT status of an Azure VM

>**Note**: This task is necessary to trigger re-evaluation of the JIT status of the Azure VM. By default, this might take up to 24 hours.

1. In the Azure portal, navigate back to the **Pricing** blade of **Azure Security Center** and switch to the **Free** tier.

    >**Note**: Wait for about 2 minutes before you proceed to the next step.

1. In the Azure portal, navigate back to the **Security Center \| Getting started** blade and switch back to the **Standard** tier.
1. Refresh the browser window displaying the Azure portal.

### Task 4: Configure inbound RDP access to the Azure VM

1. In the Azure portal, navigate to the **Security Center** blade.
1. In the **Security Center** blade, navigate to the **Just in time VM access settings**.
1. Review the **Not configured** tab, and verify that the entry representing the **ws2019-06-vm0** Azure VM is present on that tab.
1. Enable JIT VM access for the **ws2019-06-vm0** Azure VM.
1. Modify the **JIT VM access configuration** by removing access via TPC port 22.

### Task 5: Connect to the Azure VM via JIT VM access

1. From the **ws2019-06-vm0** blade in the Azure portal, request JIT VM access.

1. When the request is approved, initiate a Remote Desktop session to the target Azure VM.

1. When prompted for credentials, specify the following values, and then select **OK**:

   *Table 6: Sign in credentials*
   
   |Setting|Value|
   |---|---|
   |Username|Student|
   |Password|Pa55w.rd|

1. Verify that you can successfully sign in to the Azure VM via Remote Desktop, and then close the Remote Desktop session.

## Exercise 6: Deprovisioning the Azure environment

### Scenario

To minimize Azure-related charges, you want to deprovision the Azure resources provisioned throughout this lab.

The main tasks for this exercise are as follows:

1. Start a PowerShell session in Cloud Shell.
1. Identify all Azure resources provisioned in the lab.

### Task 1: Start a PowerShell session in Cloud Shell

1. From the Azure portal, open a PowerShell session in the **Azure Cloud Shell** blade.
1. If this is the first time you're starting **Cloud Shell**, accept the default settings.

### Task 2: Identify all Azure resources provisioned in the lab

1. From the Cloud Shell blade, run the following command to list all resource groups created throughout this lab:

   ```pwsh
   Get-AzResourceGroup -Name 'ws2019-06-*'
   ```

1. From the Cloud Shell blade, run the following command to delete all the resource groups you created throughout this lab:

   ```pwsh
   Get-AzResourceGroup -Name 'ws2019-06-*'|Remove-AzResourceGroup -Force -AsJob
   ```

## Results

After completing this lab, you will have deployed and configured Azure VMs running Windows Server 2019 in the manner that satisfies the Contoso, Ltd. manageability and security requirements.

### Prepare for the next module

When you're finished with the lab, revert all virtual machines to their initial state.
