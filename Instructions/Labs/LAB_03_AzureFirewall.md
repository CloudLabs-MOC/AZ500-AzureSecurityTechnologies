# Lab 03: Azure Firewall

## Lab scenario

You have been asked to install Azure Firewall. This will help your organization control inbound and outbound network access which is an important part of an overall network security plan. Specifically, you would like to create and test the following infrastructure components:

- A virtual network with a workload subnet and a jump host subnet.
- A virtual machine is each subnet. 
- A custom route that ensures all outbound workload traffic from the workload subnet must use the firewall.
- Firewall Application rules that only allow outbound traffic to www.bing.com. 
- Firewall Network rules that allow external DNS server lookups.

> For all the resources in this lab, we are using the **East US** region.

## Lab objectives

In this lab, you will complete the following exercise:

- Exercise 1: Deploy and test an Azure Firewall

## Estimated timing: 40 Minutes

## Architecture Diagram

   ![image](../images/az500lab3arc.png)

# Exercise 1: Deploy and test an Azure Firewall

> For all the resources in this lab, we are using the **East (US)** region. Verify with your instructor this is region to use for you class. 

In this exercise, you will complete the following tasks:

- Task 1: Use a template to deploy the lab environment. 
- Task 2: Deploy an Azure firewall.
- Task 3: Create a default route.
- Task 4: Configure an application rule.
- Task 5: Configure a network rule. 
- Task 6: Configure DNS servers.
- Task 7: Test the firewall. 

## Task 1: Use a template to deploy the lab environment. 

In this task, you will review and deploy the lab environment. 

In this task, you will create a virtual machine by using an ARM template. This virtual machine will be used in the last exercise for this lab.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type/search **Deploy a custom template (1)** and select **Deploy a custom template (2)** from the services.

   ![image](../images/az500lab8-1.png)

1. On the **Custom deployment** blade, click the **Build your own template in the editor** option.

   ![image](../images/az500lab8-2.png)

1. On the **Edit template** blade, click **Load file**.

   ![image](../images/az500lab8-3.png)

1. Navigate to the **`C:\AllFiles\AZ500-AzureSecurityTechnologies-lab-files\Allfiles\Labs\08`** **(1)** folder, then select **template.json (2)** file and then click **Open (3)**.

    ![image](../images/az500lab8-112.png)

   >**Note**: Review the content of the template and note that it deploys an Azure VM hosting Windows Server 2016 Datacenter.

1. On the **Edit template** blade, click **Save**.

   ![image](../images/az500lab8-4.png)

1. On the **Custom deployment** blade, ensure that the following settings are configured (leave any others with their default values) and then click on **Review + create (4)**.

   |Setting|Value|
   |---|---|
   |Subscription|**Leave the default Subscription (1)**|
   |Resource group|Click **Create new**, type the name **AZ500LAB03** and then click **(OK)** **(2)**|
   |Location|**(US) East US (3)**|

   ![image](../images/az500lab8-5.png)   

    >**Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

1. Then click **Create**.

1. Wait for the deployment to complete before proceeding to the next Task. This should take about 2 minutes. 

## Task 2: Deploy the Azure firewall

In this task you will deploy the Azure firewall into the virtual network. 

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type/search **Firewalls (1)** and select **Firewalls (2)**.

   ![image](../images/lab3-ex1-t1p1.png)

1. On the **Network security | Azure Firewalls** blade, click **+ Create**.

   ![image](../images/az500lab8-7.png)

1. On the **Basics** tab of the **Create a firewall** blade, specify the following settings (leave others with their default values) and click on **Next:Tags> (11)**.

   |Setting|Value|
   |---|---|
   |Subscription|**Leave the default Subscription (1)**|   
   |Resource group|**AZ500LAB03 (2)**|
   |Name|**Test-FW01 (3)**|
   |Region|**East US (4)**|
   |Firewall SKU|**Standard (5)**|

    ![image](../images/az500lab8-8.png)  

   |Setting|Value|
   |---|---|    
   |Firewall management|**Use Firewall rules (classic) to manage this firewall (6)**|
   |Choose a virtual network|click the **Use existing (7)** option|
   |Virtual network|Select **Test-FW-VN (8)**- If you get any errror please uncheck the **Enable Firewal Management NIC** under *Firewall Management NIC* |   
   |Public IP address|click **Add new** and type the name **TEST-FW-PIP** and click **OK** **(9)**|
   |Enable Firewal Management NIC|**Uncheck the box (10)**|   

    ![image](../images/az500lab8-9.png)     

1. Naviagte to **Review + create** tab at the top.

1. Once the validation is passed ,click **Create**. 

    ![image](../images/az500lab8-10.png)  

     >**Note**: You might need to scroll up to see the **Review + create**, Wait for the deployment to complete. This should take about **`5 -10`** minutes. 

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type/search for **Resource groups (1)** and select **Resource groups (2)**.

    ![image](../images/az500lab8-11.png)  

1. On the **Resource groups** blade, in the list of resource group, click the **AZ500LAB03** entry.

    ![image](../images/az500lab8-12.png)  

     >**Note**: On the **AZ500LAB03** resource group blade, review the list of resources. You can sort by **Type**.

1. In the list of resources, search for **Test-FW01** and then click the entry representing the **Test-FW01** firewall.

    ![image](../images/az500lab8-13.png)  

1. On the **Test-FW01** blade, identify the **Private IP** address that was assigned to the firewall. Copy and paste the Private IP in a notepad.

    ![image](../images/az500lab8-14.png)  

    >**Note**: You will need this information in the next task.

## Task 3: Create a default route

In this task, you will create a default route for the **Workload-SN** subnet. This route will configure outbound traffic through the firewall.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type/search for **Route tables (1)** and select **Route tables (2)**.

    ![image](../images/az500lab8-15.png)  

1. On the **Netowrk foundation | Route tables** blade, click **+ Create**.

    ![image](../images/az500lab8-111.png)

1. On the **Create route table** blade, specify the following settings and then click on **Review+create (5)**.

   |Setting|Value|
   |---|---|
   |Subscription|**Leave the default Subscription (1)**|   
   |Resource group|**AZ500LAB03 (2)**|
   |Region| **East US (3)**|
   |Name|**Firewall-route (4)**|

   ![image](../images/az500lab8-16.png)     

1. Then click **Create**, and wait for the provisioning to complete. 

1. Click on **Go to resource**.

1. Search for **Firewall-route** and then select **Firewall-route**.

1. On the **Firewall-route** blade, in the **Settings** section, click **Subnets (1)** and then, on the **Firewall-route \| Subnets** blade, click **+ Associate (2)**.

    ![image](../images/az500lab8-17.png)  

1. On the **Associate subnet** blade, specify the following settings and then click on **OK (3)**.

   |Setting|Value|
   |---|---|
   |Virtual network|**Test-FW-VN (1)**|
   |Subnet|**Workload-SN (2)**|

   ![image](../images/az500lab8-18.png)     

    >**Note**: Ensure the **Workload-SN** subnet is selected for this route, otherwise the firewall won't work correctly.

1. Back on the **Firewall-route** blade, in the **Settings** section, click **Routes (1)** and then click **+ Add (2)**. 

    ![image](../images/az500lab8-19.png)  

1. On the **Add route** blade, specify the following settings and then click on **Add (6)**. 

   |Setting|Value|
   |---|---|
   |Route name|**FW-DG (1)**|
   |Destination type|**IP Adresses (2)**|
   |Destination IP addresses/CIDR ranges|**0.0.0.0/0 (3)**|
   |Next hop type|**Virtual appliance (4)**|
   |Next hop address|the private IP address of the firewall that you identified in the previous task **(5)**|

   ![image](../images/az500lab8-20.png)     

    >**Note**: Azure Firewall is actually a managed service, but virtual appliance works in this situation.
	
## Task 4: Configure an application rule

In this task you will create an application rule that allows outbound access to `www.bing.com`.

1. In the Azure portal, navigate back to the overview panel of **Test-FW01** firewall .

1. On the **Test-FW01** blade, in the **Settings** section, click **Rules (classic) (1)**. On the **Test-FW01 \| Rules (classic)** blade, click the **Application rule collection (2)** tab, and then click **+ Add application rule collection (3)**.

    ![image](../images/az500lab8-21.png)  

1. On the **Add application rule collection** blade, specify the following settings (leave others with their default values) and then click on **Add (9)**.

   |Setting|Value|
   |---|---|
   |Name|**App-Coll01 (1)**|
   |Priority|**200 (2)**|
   |Action|**Allow (3)**|

    - On the **Add application rule collection** blade, create a new entry in the **Target FQDNs** section with the following settings (leave others with their default values):

      |Setting|Value|
      |---|---|
      |name|**AllowGH (4)**|
      |Source type|**IP Address (5)**|
      |Source|**10.0.2.0/24 (6)**|
      |Protocol port|**http:80, https:443 (7)**|
      |Target FQDNS|Type **www.bing.com (8)**|

      ![image](../images/az500lab8-22.png)  

      >**Note**: Azure Firewall includes a built-in rule collection for infrastructure FQDNs that are allowed by default. These FQDNs are specific for the platform and can't be used for other purposes. 

## Task 5: Configure a network rule

In this task, you will create a network rule that allows outbound access to two IP addresses on port 53 (DNS).

1. In the Azure portal, navigate back to the **Test-FW01 \| Rules (classic)** blade.

1. On the **Test-FW01 \| Rules (classic) (1)** blade, click the **Network rule collection (2)** tab and then click **+ Add network rule collection (3)**.

    ![image](../images/az500lab8-23.png)  

1. On the **Add network rule collection** blade, specify the following settings (leave others with their default values) and then click on **Add (11)**.

   |Setting|Value|
   |---|---|
   |Name|**Net-Coll01 (1)**|
   |Priority|**200 (2)**|
   |Action|**Allow (3)**|

    - On the **Add network rule collection** blade, create a new entry in the **IP Addresses** section with the following settings (leave others with their default values):

      |Setting|Value|
      |---|---|
      |Name|**AllowDNS (4)**|
      |Protocol|**UDP (5)**|
      |Source type|**IP address (6)**|
      |Source Addresses|**10.0.2.0/24 (7)**|
      |Destination type|**IP address (8)**|
      |Destination Address|**209.244.0.3,209.244.0.4 (9)**|
      |Destination Ports|**53 (10)**|

      ![image](../images/az500lab8-24.png)  

      >**Note**: The destination addresses used in this case are known public DNS servers. 

## Task 6: Configure the virtual machine DNS servers

In this task, you will configure the primary and secondary DNS addresses for the virtual machine. This is not a firewall requirement. 

1. In the Azure portal, navigate back to the **AZ500LAB03** resource group.

1. On the **AZ500LAB03** blade, in the list of resources, click the **Srv-Work** virtual machine.

    ![image](../images/az500lab8-25.png)  

1. On the **Srv-Work** blade, in the **Networking** section, click **Networking Settings**. On the **Srv-Work \| Networking Settings (1)** blade, click the link next to the **Network interface (2)** entry.

    ![image](../images/az500lab8-26.png)  

1. On the network interface blade, in the **Settings** section, click **DNS servers (1)**, select the **Custom (2)** option, add the two DNS servers referenced in the network rule: `209.244.0.3` and `209.244.0.4` **(3)**, and click **Save (4)** to save the change.

    ![image](../images/az500lab8-27.png)

1. Return to the **Srv-Work** virtual machine page.

    >**Note**: Wait for the update to complete.

    >**Note**: Updating the DNS servers for a network interface will automatically restart the virtual machine to which that interface is attached, and if applicable, any other virtual machines in the same availability set.

## Task 7: Test the firewall

In this task, you will test the firewall to confirm that it works as expected.

1. In the Azure portal, navigate back to the **AZ500LAB03** resource group.

1. On the **AZ500LAB03** blade, in the list of resources, click the **Srv-Jump** virtual machine.

    ![image](../images/az500lab8-28.png)  

1. On the **Srv-Jump** blade, click connect dropdown and select **Connect**. 

    ![image](../images/az500lab8-29.png)  

1. Then on the **RDP** page, click **Download RDP File** and use it to connect to the **Srv-Jump** Azure VM via Remote Desktop.

    ![image](../images/az500lab8-30.png)  

1. If any warning popup prompts in browser for download, click on **Keep** .   

    ![image](../images/az500lab8-34.png)  

1. Click on **Open file**.

    ![image](../images/az500lab8-35.png)  

1. Click on **Connect**.

   ![image](../images/az500lab7-38.png)  

1. On the **Windows Security** pop up, click on **More choices.**

   ![image](../images/az500lab7-39.png)  

1. Select **Use a different account.**

   ![image](../images/az500lab7-40.png)  

1. When prompted to authenticate, provide the following credentials, after providing the credentials, click on **OK (3)**:

   |Setting|Value|
   |---|---|
   |User name|**localadmin (1)**|
   |Password|**Pa55w.rd1234 (2)**|

   ![image](../images/az500lab8-36.png)  

1. Click on **Yes** to connect RDP.   

    ![image](../images/az500lab8-37.png)  

    >**Note**: The following steps are performed in the Remote Desktop session to the **Srv-Jump** Azure VM. 

    >**Note**: You will connect to the **Srv-Work** virtual machine. This is being done so we can test the ability to access the bing.com website.  

1. Within the Remote Desktop session to **Srv-Jump**, right-click **Start**, in the right-click menu, click **Run**.

    ![image](../images/az500lab8-38.png)  

1. From the **Run** dialog box, run the following **(1)** to connect to **Srv-Work** and then click on **OK (2)** 

    ```
    mstsc /v:Srv-Work
    ```

    ![image](../images/az500lab8-39.png)  

1. When prompted to authenticate, provide the following credentials and then click on **OK (3)**.

   |Setting|Value|
   |---|---|
   |User name|**localadmin (1)**|
   |Password|**Pa55w.rd1234 (2)**|

   ![image](../images/az500lab8-31.png)  

    >**Note**: Wait for the Remote Desktop session to be established and the Server Manager interface to load.
    
1. Click on **Yes**, If you recieve any pop up.
   
1. Within the Remote Desktop session to **Srv-Work**, in **Server Manager**, click **Local Server (1)** and then click on **On (2)** corresponding to **IE Enhanced Security Configuration**.

    ![image](../images/az500lab8-40.png)  

1. In the **Internet Explorer Enhanced Security Configuration** dialog box, set both options to **Off (1)(2)** and click **OK (3)**.

    ![image](../images/az500lab8-41.png)  

1. Within the Remote Desktop session to **Srv-Work**, start Internet Explorer and browse to **`https://www.bing.com`**. 

    ![image](../images/az500lab8-42.png)

    >**Note**: Click on **Ask me later**, If you recieve any pop up.   

    >**Note**: The website should successfully display. The firewall allows you access.

1. Browse to **`http://www.microsoft.com/`**

    ![image](../images/az500lab8-43.png)  

    >**Note**: Within the browser page, you should receive a message with text resembling the following: `Action: Deny. No rule matched. Proceeding with default action.` This is expected, since the firewall blocks access to this website. 

1. Terminate both Remote Desktop sessions.

> **Result:** You have successfully configured and tested the Azure Firewall.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="d7fc707d-6388-4dd1-80f2-5092ff0d75a8" />

### You have successfully completed the lab.

