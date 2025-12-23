# Lab 02: Network Security Groups and Application Security Groups

## Lab scenario
You have been asked to implement your organization's virtual networking infrastructure and test to ensure it is working correctly. In particular:

- The organization has two groups of servers: Web Servers and Management Servers.
- Each group of servers should be in its own Application Security Group. 
- You should be able to RDP into the Management Servers, but not the Web Servers.
- The Web Servers should display the IIS web page when accessed from the internet. 
- Network security group rules should be used to control network access.  

 > For all the resources in this lab, we are using the **East US** region.

## Lab objectives
In this lab, you will complete the following exercises:
- Exercise 1: Create the Virtual networking infrastructure
- Exercise 2: Deploy Virtual machines and test the network filters

## Estimated timing: 45 Minutes

## Architecture Diagram

   ![image](../images/az500lab2arc.png)

# Exercise 1: Create the Virtual networking infrastructure

In this exercise, you will complete the following tasks:

- Task 1: Create a Virtual network with one subnet.
- Task 2: Create two application security groups.
- Task 3: Create a network security group and associate it with the virtual network subnet.
- Task 4: Create inbound NSG security rules to all traffic to web servers and RDP to the management servers.

## Task 1:  Create a Virtual network

In this task, you will create a virtual network to use with the network and application security groups. 

1. On the Azure portal locate the search bar at the top of the page. Search for **Virtual networks (1)**, select the search result for **Virtual networks (2)**.

   ![image](../images/az500lab7-1.png)

1. On the **Network foundation | Virtual networks** blade, click **+ Create**.

   ![image](../images/az500lab7-2.png)

1. On the **Basics** tab of the **Create virtual network** blade, specify the following settings (leave others with their default values) and then naviate to **IP Addresses (5)**:

    |Setting|Value|
    |---|---|
    |Subscription|**Leave the default subscription (1)**|
    |Resource group|click **Create new** and type the name **AZ500LAB02** then click **OK** **(2)**|
    |Name|**myVirtualNetwork (3)**|
    |Region|**East US (4)**|

    ![image](../images/az500lab7-3.png)

1. On the **IP addresses** tab of the **Create virtual network** blade, set the **IPv4 address space** to **10.0.0.0/16 (1)**, if it is not yet set and if needed. In the **Subnet name** column, click **default (2)**, on the **Edit subnet** blade, specify the following settings and click **Save (5)**:

    |Setting|Value|
    |---|---|
    |Subnet name|**default (3)**|
    |Size|**/24 (256 addresses) (4)**|

    >**Note:** If the **default** subnet is not there in the subnet section, then you have to create it. Click on **+ Add subnet**, then follow this given instructions, and click on **Add**.

    ![image](../images/az500lab7-111.png)

1. Back on the **IP addresses** tab of the **Create virtual network** blade, click **Review + create**.

   ![image](../images/az500lab7-4.png)

1. On the **Review + create** tab of the **Create virtual network** blade and then click **Create**.

   ![image](../images/az500lab7-112.png)

## Task 2:  Create application security groups

In this task, you will create an application security group.

1. On the Azure portal locate the search bar at the top of the page, and search for **Application security groups (1)** select the search result for **Application security groups (2)**.

   ![image](../images/az500lab7-5.png)

1. On the **Network foundation | Application security groups** blade, click **+ Create**.

   ![image](../images/az500lab7-113.png)

1. On the **Basics** tab of the **Create an application security group** blade, specify the following settings and then click on **Review + create (5)**.

    |Setting|Value|
    |---|---|
    |Subscription|**Leave the default subscription (1)**|    
    |Resource group|**AZ500LAB02 (2)**|
    |Name|**myAsgWebServers (3)**|
    |Region|**East US (4)**|

    ![image](../images/az500lab7-6.png)    

     >**Note**: This group will be for the web servers.

1. Then click on **Create**.

   ![image](../images/az500lab7-114.png)

1. Navigate back to the **Network foundation | Application security groups** blade and click **+ Create**.

    ![image](../images/az500lab7-7.png)

1. On the **Basics** tab of the **Create an application security group** blade, specify the following settings and then click on **Review + create (5)**.

    |Setting|Value|
    |---|---|
    |Subscription|**Leave the default subscription (1)**|       
    |Resource group|**AZ500LAB02 (2)**|
    |Name|**myAsgMgmtServers (3)**|
    |Region|**East US (4)**|

    ![image](../images/az500lab7-8.png)       

     >**Note**: This group will be for the management servers.

1. Then click **Create**.

   ![image](../images/az500lab7-115.png)

## Task 3:  Create a network security group and associate the NSG to the subnet

In this task, you will create a network security group. 

1. On the Azure portal locate the search bar at the top of the page search for **Network security groups (1)**, select the search result for **Network security groups (2)**.

    ![image](../images/az500lab7-9.png)   

1. On the **Network foundaton | Network security groups** blade, click **+ Create**.

   ![image](../images/az500lab7-116.png)

1. On the **Basics** tab of the **Create network security group** blade, specify the following settings and then click on **Review + create (5)**.

    |Setting|Value|
    |---|---|
    |Subscription|**Leave the default subscription (1)**|
    |Resource group|**AZ500LAB02 (2)**|
    |Name|**myNsg (3)**|
    |Region|**East US (4)**|

    ![image](../images/az500lab7-10.png)       

1. Then click **Create**.

   ![image](../images/az500lab7-117.png)

1. After deployment succeeded, click on **Go to resource**.

   ![image](../images/az500lab7-118.png)

1. On the **myNsg** blade, in the **Settings** section, click **Subnets (1)** and then click **+ Associate (2)**.

    ![image](../images/az500lab7-11.png)   

1. On the **Associate subnet** blade, specify the following settings and click **OK (3)**:

    |Setting|Value|
    |---|---|
    |Virtual network|**myVirtualNetwork (1)**|
    |Subnet|**default (2)**|
    
    ![image](../images/az500lab7-12.png)

## Task 4: Create inbound NSG security rules to all traffic to web servers and RDP to the servers. 

1. On the **myNsg** blade, in the **Settings** section, click **Inbound security rules (1)**. Review the default inbound security rules and then click **+ Add (2)**.

    ![image](../images/az500lab7-13.png)   

1. On the **Add inbound security rules** blade, specify the following settings to allow TCP ports 80 and 443 to the **myAsgWebServers** application security group (leave all other values with their default values) and then click on **Add (7)**.

    |Setting|Value|
    |---|---|
    |Destination|select **Application security group (1)** from the drop down|
    |Destination application security groups| select **myAsgWebServers (2)** from the drop down |
    |Destination port ranges|**80,443 (3)**|
    |Protocol|**TCP (4)**|
    |Priority|**100 (5)**|                                                    
    |Name|**Allow-Web-All (6)**|

    ![image](../images/lab2-ex4-t1p1.png)       

1. On the **myNsg** blade, in the **Settings** section, click **Inbound security rules (1)**, and then click **+ Add (2)**.

    ![image](../images/az500lab7-13.png)   

1. On the **Add inbound security rules** blade, specify the following settings to allow the RDP port (TCP 3389) to the **myAsgMgmtServers** application security group (leave all other values with their default values) and then click on **Add (6)**. 

    |Setting|Value|
    |---|---|
    |Destination|select **Application security group (1)** from the drop down|
    |Destination application security groups |**myAsgMgmtServers (2)** from the drop down|    
    |Service|Choose **RDP (3)**|      
    |Destination port ranges| **3389**|
    |Protocol|**TCP** |
    |Priority|**110 (4)**|                                                
    |Name|**Allow-RDP-All (5)**|

    ![image](../images/lab2-ex4-t1p2.png)     

     > Result: You have deployed a virtual network, network security with inbound security rules, and two application security groups. 

# Exercise 2: Deploy virtual machines and test network filters

In this exercise, you will complete the following tasks:

- Task 1: Create a virtual machine to use as a web server.
- Task 2: Create a virtual machine to use as a management server. 
- Task 3: Associate each virtual machines network interface to it's application security group.
- Task 4: Test the network traffic filtering.

## Task 1: Create a virtual machine to use as a web server.

In this task, you will create a virtual machine to use as a web server.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Virtual machines (1)** and select **Virtual machines (2)** from the services.

    ![image](../images/az500lab7-16.png)   

1. On the **Computer infrastructure | Virtual machines** blade, click **+ Create (1)** and, in the dropdown list, click **Virtual machine (2)**.

    ![image](../images/lab2-ex2-t1p1.png)

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values) and then click on **Next: Disks >(15)**.

   |Setting|Value|
   |---|---|
   |Subscription|**Leave the default subscription (1)**|
   |Resource group|**AZ500LAB02 (2)**|
   |Virtual machine name|**myVmWeb (3)**|
   |Region|**(US)East US (4)**|
   |Availability options|**No infrastructure redundancy required (5)**|
   |Security type|**Standard (6)**|
   |Image|**Windows Server 2022 Datacenter: Azure Edition- x64 Gen2 (7)**|
   |Size|**Standard D2s v3 (8)**|
 
    ![image](../images/az500lab7--18.png)
    
   |Setting|Value|
   |---|---|
   |Username|**Student (9)**|
   |Password|**Pa55w.rd1234 (10)**|
   |Confirm password|**Pa55w.rd1234 (11)**|
   |Public inbound ports|**None (12)**| 
   |Would you like to use an existing Windows Server License |**check the box (13)**|
   |I confirm I have an eligible Windows Server license with Software Assurance or Windows Server subscription to apply this Azure Hybrid Benefit| **Check the box (14)**|   

    ![image](../images/az500lab7--23.png)
 
     >**Note**: For public inbound ports, we will rely on the precreated NSG. 

1. On the **Disks** tab of the **Create a virtual machine** blade, set the **OS disk type** to **Standard HDD (1)** and click **Next: Networking > (2)**.

   ![image](../images/lab2-ex2-t1p2.png)

1. On the **Networking** tab of the **Create a virtual machine** blade, select the previously created network **myVirtualNetwork (1)**. Under **NIC network security group** select **None (2)** and then click on **Next: Management > (3)**.

   ![image](../images/az500lab7-20.png)

   > **Note:** Make sure **default** subnet with **myVmWeb-ip** Public IP is selected.

1. On the **Management** tab, click on **Next: Monitoring>**.

1. On the **Monitoring** tab of the **Create a virtual machine** blade, verify the following setting and then click on **Review + create (2)**.

   |Setting|Value|
   |---|---|
   |Boot diagnostics|**Enabled with managed storage account (recommended) (1)**|

   ![image](../images/az500lab7-21.png)   

1. Ensure that validation was successful and click on **Create** to proceed.

   ![image](../images/az500lab7-23.png)

## Task 2: Create a virtual machine to use as a management server. 

In this task, you will create a virtual machine to use as a management server.

1. In the Azure portal, navigate back to the **Compute infrastructure | Virtual machines** blade, click **+ Create (1)**, and, in the dropdown list, click **Virtual machine (2)**.

   ![image](../images/az500lab7-119.png)

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values) and then click on **Next: Disks > (15)**

   |Setting|Value|
   |---|---|
   |Subscription|**Leave the deafult subscription (1)**|
   |Resource group|**AZ500LAB02 (2)**|
   |Virtual machine name|**myVMMgmt (3)**|
   |Region|**(US)East US (4)**|
   |Availability options|**No infrastructure redundancy required (5)**|
   |Security type|**Standard (6)**|   
   |Image|**Windows Server 2022 Datacenter: Azure Edition- Gen2 (7)**|
   |Size|**Standard D2s v3 (8)**|

    ![image](../images/az500lab7-24.png)

   |Setting|Value|
   |---|---|
   |Username|**Student (9)**|
   |Password|**Pa55w.rd1234 (10)**|
   |Confirm password|**Pa55w.rd1234 (11)**|
   |Public inbound ports|**None (12)**| 
   |Would you like to use an existing Windows Server License |**check the box (13)**|
   |I confirm I have an eligible Windows Server license with Software Assurance or Windows Server subscription to apply this Azure Hybrid Benefit| **Check the box (14)**|   

    ![image](../images/az500lab7--23.png)    

    >**Note**: For public inbound ports, we will rely on the precreated NSG. 

1. On the **Disks** tab of the **Create a virtual machine** blade, set the **OS disk type** to **Standard HDD (1)** and click **Next: Networking > (2)**.

   ![image](../images/az500lab7--26.png)

1. On the **Networking** tab of the **Create a virtual machine** blade, select the previously created network **myVirtualNetwork (1)** along with Subnet as **default (2)** and Public IP **myVMMgmt-ip (3)**. Under **NIC network security group**, select **None (4)** and then navigate to **Monitoring (5)** tab.

   ![image](../images/az500lab7-27.png)

1. On the **Monitoring** tab of the **Create a virtual machine** blade, verify the following setting and then click on **Review + create (2)**.

   |Setting|Value|
   |---|---|
   |Boot diagnostics|**Enabled with managed storage account (recommended) (1)**|

   ![image](../images/az500lab7-28.png)   

1. Ensure that validation was successful and click **Create**.

   ![image](../images/az500lab7-120.png)

    >**Note**: Wait for both virtual machines to be provisioned before continuing. 

## Task 3: Associate each virtual machines network interface to its application security group.

In this task, you will associate each virtual machine's network interface with the corresponding application security group. The myVmWeb virtual machine interface will be associated to the myAsgWebServers ASG. The myVMMgmt virtual machine interface will be associated to the myAsgMgmtServers ASG. 

1. In the Azure portal, navigate back to the **Compute infrastructure | Virtual machines** blade and verify that both virtual machines are listed with the **Running** status.

   ![image](../images/az500lab7-29.png)    

1. In the list of virtual machines, click the **myVmWeb** virtual machine.

   ![image](../images/az500lab7--30.png)  

1. On the **myVmWeb** blade, in the **Networking** section, select **Application security groups (1)** an then click on **+ Add application security groups (2)**.

   ![image](../images/lab2-ex2-t2p1.png)  

1. On the **Add Application security groups** select **myAsgWebServers (1)**, and then click **Add (2)**.

   ![image](../images/az500lab7-32.png)  

1. Navigate back to the **Compute infrastructure | Virtual machines** blade and in the list of virtual machines, click the **myVMMgmt** virtual machine.

1. On the **myVMMgmt** blade, in the **Networking** section, select **Application security groups (1)** an then click on **+ Application security groups (2)**.

   ![image](../images/az500lab7-33.png)  

1. On the **Add Application security groups** select **myAsgMgmtServers (1)**, and then click **Add (2)**.

   ![image](../images/az500lab7-34.png)  


## Task 4: Test the network traffic filtering

In this task, you will test the network traffic filters. You should be able to RDP into the myVMMgmnt virtual machine. You should be able to connect from the internet to the myVmWeb virtual machine and view the default IIS web page.  

1. Navigate back to the **myVMMgmt** virtual machine blade.

1. On the **myVMMgmt** blade, click **Connect (1)** drop down and select **Connect (2)** from the drop down list. 

   ![image](../images/az500lab7-35.png)  

1. Click on **Download RDP File**.

   ![image](../images/lab1-17.png)

1. Click on **Keep**, for the pop up that appears.   

   ![image](../images/az500lab7-36.png)  

1. Click on **Open file** from the top right corner.

   ![image](../images/az500lab7-37.png)  

1. Click on **Connect**.

   ![image](../images/az500lab7-38.png)  

1. On the **Windows Security** pop up, click on **More choices.**

   ![image](../images/az500lab7-39.png)  

1. Select **Use a different account.**

   ![image](../images/az500lab7-40.png)  

1. Open that file and use it to connect to the **myVMMgmt** Azure VM via Remote Desktop. When prompted to authenticate, provide the following credentials, after providing the credentials, click on **OK (3)**:

   |Setting|Value|
   |---|---|
   |User name|**Student (1)**|
   |Password|**Pa55w.rd1234 (2)**|

   ![image](../images/az500lab7-41.png)     

    >**Note**: Verify that the Remote Desktop connection was successful. At this point you have confirmed you can connect via Remote Desktop to myVMMgmt.

1. Click on **Yes** to connect RDP.

   ![image](../images/az500lab7-42.png)  

1. In the Azure portal, navigate to the **myVmWeb** virtual machine blade.

1. On the **myVmWeb** blade, in the **Operations** section, click **Run command (1)** and then click **RunPowerShellScript (2)**.

   ![image](../images/az500lab7-43.png)  

1. On the **Run Command Script** pane, run the following to install the Web server role on **myVmWeb**:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

     ![image](../images/lab01-2.png)

    >**Note**: Wait for the installation to complete. This might take a couple of minutes. At that point, you can verify that myVmWeb can be accessed via HTTP/HTTPS.

1. After getting the output, close the **Run Command Script** blade, and navigate back to the **Overview (1)** page of **myVmWeb** blade. Copy and paste the **Public IP address (2)** of the myVmWeb Azure VM to the notepad.

   ![image](../images/az500lab7-45.png)  

1. Open another browser tab and navigate to IP address you identified in the previous step.

   ![image](../images/lab1-1.png)

    >**Note**: The browser page should display the default IIS welcome page because port 80 is allowed inbound from the internet based on the setting of the **myAsgWebServers** application security group. The network interface of the myVmWeb Azure VM is associated with that application security group. 

> **Result:** You have validated that the NSG and ASG configuration is working and traffic is being correctly managed.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If you receive a success message, you can proceed to the next task.
   - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
 
   <validation step="63a375cc-ae94-418b-a78c-4561f35ff40d" />

## Summary
In this lab, you have completed the following:

* Created a virtual network and subnet
* Created **Application Security Groups (ASGs)** for web servers and management servers
* Created and associated a **Network Security Group (NSG)** with the subnet
* Configured NSG rules to allow **HTTP/HTTPS** to web servers and **RDP** to management servers
* Deployed web and management virtual machines
* Associated VM network interfaces with their respective ASGs
* Verified network filtering by testing RDP access and IIS web access

### You have successfully completed the lab
