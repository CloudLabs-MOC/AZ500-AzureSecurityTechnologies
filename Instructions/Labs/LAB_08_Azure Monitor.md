# Lab 08: Create a Log Analytics Workspace, Azure Storage Account, and Data Collection Rule (DCR)

## Lab scenario
You have been asked to create a proof of concept for monitoring virtual machine performance. Specifically, you want to:
- Configure a virtual machine such that telemetry and logs can be collected.
- Show what telemetry and logs can be collected.
- Show how the data can be used and queried. 

> For all the resources in this lab, we are using the **East US** region.

## Lab objectives
In this lab, you will complete the following exercise:
- Exercise 1: Collect data from an Azure virtual machine with Azure Monitor

## Estimated timing: 45 minutes

## Architecture Diagram

![image](../images/archtech10.png)

# Exercise 1: Collect data from an Azure virtual machine with Azure Monitor

In this exercise, you will complete the following tasks: 

- Task 1: Deploy an Azure virtual machine 
- Task 2: Create a Log Analytics workspace
- Task 3: Enable the Log Analytics virtual machine extension
- Task 4: Collect virtual machine event and performance data

## Task 1: Deploy an Azure virtual machine

1. From the Azure portal, open the **Azure Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

    ![](../images/unit6-image1.png)

1. The first time you open the Cloud Shell, you may be prompted to choose the type of shell you want to use (*Bash* or *PowerShell*). If so, select **Powershell**.

    ![](../images/pwershell1.png)
   
1. On the Getting started, select **No storage account required (1)** and select your **Subscription (2)** under storage account subscription. Click on **Apply (3)**.

     ![](../images/New-image110.png)

1. In the upper-left menu of the Cloud Shell pane, make sure you are using **Powershell**. If not selected select **Switch to Powershell**. In **Switch to Powershell in Cloud Shell** pop-up select **Confirm**.

1. In the PowerShell session within the Cloud Shell pane, run the following to create a resource group that will be used in this lab:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB080910 -Location 'EastUS'
    ```

1. In the PowerShell session within the Cloud Shell pane, run the following to create a new Azure virtual machine. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB080910" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -OpenPorts 80,3389
    ```

1.  When prompted for credentials:

    |Setting|Value|
    |---|---|
    |User name|**localadmin**|
    |Password|**Pa55w.rd1234**|

    ![](../images/br14.png)

     >**Note**: Wait for the deployment to complete. 

1. In the PowerShell session within the Cloud Shell pane, run the following to confirm that the virtual machine named **myVM** was created and its **ProvisioningState** is **Succeeded**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB080910' | Format-Table
    ```

     ![](../images/br15.png)    

1. Close the Cloud Shell pane. 

## Task 2: Create a Log Analytics workspace

In this task, you will create a Log Analytics workspace. 

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Log Analytics workspaces (1)** and select **Log Analytics workspaces (2)** from the services.

   ![](../images/New-image111.png)

1. On the **Log Analytics workspaces** blade, click **+ Create**.

1. On the **Basics** tab of the **Create Log Analytics workspace** blade, specify the following settings (leave others with their default values) and then click on **Review+create (5)**.

    |Setting|Value|
    |---|---|
    |Subscription|**Leave the default Subscription (1)**|
    |Resource group|Select **AZ500LAB080910 (2)**|
    |Name| Enter **LogAnalytics<inject key="DeploymentID" enableCopy="false"/> (3)**|
    |Region|**(US) East US (4)**|

    ![](../images/br17.png)
   
1. On the **Review + create** tab of the **Create Log Analytics workspace** blade, click **Create**.

## Task 3: Create an Azure storage account

In this task, you will create a storage account.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Storage accounts (1)** and select **Storage accounts (2)** from the services.

   ![](../images/New-image113.png)

2. On the **Storage accounts** blade in the Azure portal, click the **+ Create** button to create a new storage account.

    ![](../images/New-image114.png)

3. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their default values) and then click on **Review+create (7)**.

    |Setting|Value|
    |---|---|
    |Subscription|**Leave the default Subscription (1)**|
    |Resource group|Select **AZ500LAB080910 (2)**|
    |Storage account name|Enter **storage<inject key="DeploymentID" enableCopy="false"/> (3)**|
    |Location|**(US) EastUS (4)**|
    |Performance|**Standard (general-purpose v2 account) (5)**|
    |Redundency|**Locally redundant storage (LRS) (6)**|

    ![](../images/New-image(115).png)
   
4. Wait for the validation process to complete, and click **Create**.

    >**Note**: Wait for the Storage account to be created. This should take about 2 minutes.

## Task 4: Create a Data Collection Rule.

In this task, you will create a data collection rule.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Monitor (1)** and select **Monitor (2)** from the services.

      ![](../images/New-image116.png)
   
1. On the **Monitor Settings** blade, click **Data Collection Rules (1) > Create (2)**.

   ![](../images/New-image117.png)

1. On the **Basics** tab of the **Create Data Collection Rule** blade, specify the following settings and then click on **Next: Resources > (6)**.
  
    |Setting|Value|
    |---|---|
    |**Rule details**|
    |Rule Name|**DCR1 (1)**|
    |Subscription|**Leave the default Subscription (2)**|
    |Resource Group|Select **AZ500LAB080910 (3)**|
    |Region|**East US (4)**|
    |Platform Type|**Windows (5)**|
    |Data Collection Endpoint|*Leave Blank*|

    ![](../images/New-image118.png)
  
1. On the Resources tab,check the box for **Enable Data Collection Endpoints (1)**. Select **+ Add resources (2)**  In the Select a scope template, check **AZ500LAB080910 (3)** and click **Apply (4)**.

    ![](../images/az500lab13-1.png)

1. Click on the button labeled **Next: Collect and deliver >** to proceed.

1. Click **+ Add data source (1)**, then on the **Add data source** page, change the **Data source type** drop-down menu to display **Performance Counters (2).** Leave the following default settings **(3)** and click on the button labeled **Next: Destination > (4)** to proceed.

    |Setting|Value|
    |---|---|
    |**Performance counter**|**Sample rate (seconds)**|
    |CPU|60|
    |Memory|60|
    |Disk|60|
    |Network|60|

     ![](../images/br18.png)    

1. Click on **+Add destination (1)**, change the **Destination type** drop-down menu to display **Azure Monitor Logs (2).** In the **Subscription** window, ensure that your *Subscription* is displayed **(3)**, then change the **Destination details** drop-down menu to reflect your previously created Log Analytics Workspace **(4)**. Click on **Add data source (5)**. 

     ![](../images/br19.png) 

1. Click **Review + create.**

     ![](../images/New-image121.png)

1. Click **Create.**

> Results: You deployed an Azure virtual machine, Log Analytics workspace, Azure storage account, and a data collection rule to collect events and performance counters from virtual machines with Azure Monitor Agent.

>**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Defender for Cloud lab and the Microsoft Sentinel lab.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If you receive a success message, you can proceed to the next task.
   - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
 
   <validation step="2438c3db-e10e-4895-88cd-b6f1ffa433ca" />

### You have successfully completed the lab, click on Next to start the next lab

 
