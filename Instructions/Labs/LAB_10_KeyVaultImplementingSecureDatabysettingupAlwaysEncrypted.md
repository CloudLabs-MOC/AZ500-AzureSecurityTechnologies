# Lab 10: Key Vault (Implementing Secure Data by setting up Always Encrypted)
# Student lab manual

## Lab scenario

You have been asked to create a proof of concept application that makes use of the Azure SQL Database support for Always Encrypted functionality. All of the secrets and keys used in this scenario should be stored in Key Vault. The application should be registered in Azure Active Directory (Azure AD) in order to enhance its security posture. To accomplish these objectives, the proof of concept should include:

- Creating an Azure Key Vault and storing keys and secrets in the vault.
- Create a SQL Database and encrypting content of columns in database tables by using Always Encrypted.

>**Note**: For all the resources in this lab, we are using the **East US** region. Verify with your instructor this is the region to use for class. 

To keep the focus on the security aspects of Azure, related to building this proof of concept, you will start from an automated ARM template deployment, setting up a Virtual Machine with Visual Studio 2019 and SQL Server Management Studio 2019.

## Lab objectives

In this lab, you will complete the following exercises:

- Exercise 1: Deploy the base infrastructure from an ARM template
- Exercise 2: Configure the Key Vault resource with a key and a secret
- Exercise 3: Configure an Azure SQL database and a data-driven application
- Exercise 4: Demonstrate the use of Azure Key Vault in encrypting the Azure SQL database

## Lab files:

- **C:\\AllFiles\\AZ500-AzureSecurityTechnologies-prod\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **C:\AllFiles\AZ500-AzureSecurityTechnologies-prod\Allfiles\Labs\\10\\program.cs**

## Estimated timing: 60 minutes

## Architecture diagram

![](../Labs/Lab-Scenario-Preview/media/AZ-500-LSP-Mod-3-1.png)

### Exercise 1: Deploy the base infrastructure from an ARM template

In this exercise, you will complete the following tasks:

- Task 1: Deploy an Azure VM and an Azure SQL database

#### Task 1: Deploy an Azure VM and an Azure SQL database

In this task, you will deploy an Azure VM, which will automatically install Visual Studio 2019 and SQL Server Management Studio 2019 as part of the deployment.

1. If you are not already signed into the Azure portal, sign in to the Azure portal at https://portal.azure.com, with the Azure credentials.

2. On **Sign in to Microsoft Azure** blade, you will see a login screen, in that enter the following email/username and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>

3. Now enter the following password and click on **Sign in**.
   * Password: <inject key="AzureAdUserPassword"></inject>

4. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Deploy a custom template** and press the **Enter** key.

5. On the **Custom deployment** blade, click the **Build your own template in the editor** option.

6. On the **Edit template** blade, click **Load file**, locate the **C:\\AllFiles\\AZ500-AzureSecurityTechnologies-prod\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** file and click **Open**.

7. On the **Edit template** blade, click **Save**.

8. On the **Custom deployment** blade, under **Project details**  ensure that the following settings are configured (leave any others with their default values):

   |Setting|Value|
   |---|---|
   |Subscription|Let it be default|
   |Resource group|Select **AZ500LAB10-<inject key="DeploymentID" enableCopy="false"/>**|
   |Location|**East US**|
   |Admin Username|**Student**|
   |Admin Password|**Pa55w.rd1234**|
   
    >**Note**: While you can change the administrative credentials used for logging on to the Virtual Machine, you don't have to.

    >**Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

9. Click the **Review and Create** button, and confirm the deployment by clicking the **Create** button. 

    >**Note**: This initiates the deployment of the Azure VM and Azure SQL Database required for this lab. 

    >**Note**: Do not wait for the ARM template deployment to be completed, continue on to the next exercise. The deployment might take upto **20-25 minutes**. 
    
> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- Click the Lab Validation tab located at the upper right corner of the lab guide section and navigate to the Lab Validation Page.
- Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.


### Exercise 2: Configure the Key Vault resource with a key and a secret

>**Note**: For all the resources in this lab, we are using the **East (US)** region. Verify with your instructor this is region to use for you class. 

In this exercise, you will complete the following tasks:

- Task 1: Create and configure a Key Vault
- Task 2: Add a key to the Key Vault
- Task 3: Add a secret to the Key Vault

#### Task 1: Create and configure a Key Vault

In this task, you will create an Azure Key Vault resource. You will also configure the Azure Key Vault permissions.

1. The first time you open the Cloud Shell, you may be prompted to choose the type of shell you want to use (*Bash* or *PowerShell*). Select **PowerShell**. If you do not see this option, skip the step.  

1. If you are prompted to create storage for your Cloud Shell, ensure your subscription is selected and click on **show advanced settings**. Please make sure you have selected your resource group **AZ500LAB10-<inject key="DeploymentID" enableCopy="false"/>** and in **storage account** select Create new and enter **storage<inject key="DeploymentID" enableCopy="false"/>** for the **Storage account name** and enter **fileshare<inject key="DeploymentID" enableCopy="false"/>** For the **File share name**, then click on **Create Storage**.

1. Ensure **PowerShell** is selected in the drop-down menu in the upper-left corner of the Cloud Shell pane.

1. In the PowerShell session within the Cloud Shell pane, run the following to create an Azure Key Vault in the resource group **AZ500LAB10-<inject key="DeploymentID" enableCopy="false"/>**. The Key Vault name must be unique. Remember the name you have chosen. You will need it throughout this lab.  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10-[DeploymentID]').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10-[DeploymentID]' -Location $location
    ```

    >**Note**: In the above code snippet, replace the **[DeploymentID]** with **<inject key="DeploymentID" enableCopy="true"/>**
   
    >**Note**: The output of the last command will display the vault name and the vault URI. The vault URI is in the format `https://<vault_name>.vault.azure.net/`

1. Close the Cloud Shell pane. 

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Resource groups** and press the **Enter** key.

1. On the **Resource groups** blade, in the list of resource group, click the **AZ500LAB10-<inject key="DeploymentID" enableCopy="false"/>** entry.

1. On the Resource Group blade, click the entry representing the newly created Key Vault. 

1. On the Key Vault blade, in the **Overview** section, click **Access Policies** and then click **+ Create**.

1. On the **Create an access policy** blade, specify the following settings (leave all others with their default values): 

    |Setting|Value|
    |----|----|
    |Configure from template (optional)|**Key, Secret, & Certificate Management**|
    |Key permissions|click **Select all** permissions (Make sure the permissions for **Rotation Policy Operations** are **unchecked**) |
    |Secret permissions|click on **Select all** check boxes resulting in total of **7 selected** permissions|
    |Certification permissions|click on **Select all** check boxes resulting in total of **15 selected** permissions|
    |Cryptographic Operations|Select **Unwrap key** , **Wrap Key** , **Verify** , **Sign**|
    
1. Now click on **Next** to reach to **Principal** tab.
    
1. On the **Principal** blade, select your user account, and click **Select** and click on **Review + create** tab and then **Create**.

#### Task 2: Add a key to Key Vault

In this task, you will add a key to the Key Vault and view information about the key. 

1. In the Azure portal, open a PowerShell session in the Cloud Shell pane.

1. Ensure **PowerShell** is selected in the upper-left drop-down menu of the Cloud Shell pane.

1. In the PowerShell session within the Cloud Shell pane, run the following to add a software-protected key to the Key Vault: 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10-[DeploymentID]'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```
    >**Note**: In the above code snippet, replace the **[DeploymentID]** with **<inject key="DeploymentID" enableCopy="true"/>**
    
    >**Note**: The name of the key is **MyLabKey**

1. In the PowerShell session within the Cloud Shell pane, run the following to verify the key was created:

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

1. In the PowerShell session within the Cloud Shell pane, run the following to display the key identifier:

    ```powershell
    $key.key.kid
    ```

1. Minimize the Cloud Shell pane. 

1. Back in the Azure portal, on the Key Vault blade, in the **Objects** section, click **Keys**.

1. In the list of keys, click the **MyLabKey** entry and then, on the **MyLabKey** blade, click the entry representing the current version of the key.

    >**Note**: Examine the information about the key you created.

    >**Note**: You can reference any key by using the key identifier. To get the most current version, reference `https://<key_vault_name>.vault.azure.net/keys/MyLabKey` or get the specific version with: `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### Task 3: Add a Secret to Key Vault

1. Switch back to the Cloud Shell pane.

1. In the PowerShell session within the Cloud Shell pane, run the following to create a variable with a secure string value:

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

1.  In the PowerShell session within the Cloud Shell pane, run the following to add the secret to the vault:

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**Note**: The name of the secret is SQLPassword. 

1.  In the PowerShell session within the Cloud Shell pane, run the following to verify the secret was created.

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

1. Minimize the Cloud Shell pane. 

1. In the Azure portal, navigate back to the Key Vault blade. In the **Objects** section, click **Secrets**.

1. In the list of secrets, click the **SQLPassword** entry and then, on the **SQLPassword** blade, click the entry representing the current version of the secret.

    >**Note**: Examine the information about the secret you created.

    >**Note**: To get the most current version of a secret, reference `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>` or get a specific version, reference `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>`
    
> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- Click the Lab Validation tab located at the upper right corner of the lab guide section and navigate to the Lab Validation Page.
- Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

### Exercise 3: Configure an Azure SQL database and a data-driven application

In this exercise, you will complete the following tasks:

- Task 1: Enable a client application to access the Azure SQL Database service.
- Task 2: Create a policy allowing the application access to the Key Vault.
- Task 3: Retrieve SQL Azure database ADO.NET Connection String 
- Task 4: Log on to the Azure VM running Visual Studio 2019 and SQL Management Studio 2019
- Task 5: Create a table in the SQL Database and select data columns for encryption

#### Task 1: Enable a client application to access the Azure SQL Database service

In this task, you will enable a client application to access the Azure SQL Database service. This will be done by setting up the required authentication and acquiring the Application ID and Secret that you will need to authenticate your application. T

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **App Registrations** and press the **Enter** key.

1. On the **App Registrations** blade, click **+ New registration**. 

1. On the **Register an application** blade, specify the following settings (leave all others with their default values):

    |Setting|Value|
    |----|----|
    |Name|**sqlApp**|
    |Redirect URI (optional)|**Web** and **https://sqlapp**|

1. Now click **Register**. 

    >**Note**: Once the registration is completed, the browser will automatically redirect you to **sqlApp** blade. 

1. On the **sqlApp** blade, identify the value of **Application (client) ID**. 

    >**Note**: Record this value. You will need it in the next task.

1. On the **sqlApp** blade, in the **Manage** section, click **Certificates & secrets**.

1. On the **sqlApp | Certificates & secrets** blade / **Client Secrets** section, click **+ New client secret**

1. In the **Add a client secret** pane, specify the following settings:

    |Setting|Value|
    |----|----|
    |Description|**Key1**|
    |Expires|**365 days (12 months)**|
	
1. Click **Add** to update the application credentials.

1. On the **sqlApp | Certificates & secrets** blade, identify the value of **Key1**.

    >**Note**: Record this value. You will need it in the next task. 

    >**Note**: Make sure to copy the value *before* you navigate away from the blade. Once you do, it is no longer possible to retrieve its clear text value.

#### Task 2: Create a policy allowing the application access to the Key Vault.

In this task, you will grant the newly registered app permissions to access secrets stored in the Key Vault.

1. In the Azure portal, open a PowerShell session in the Cloud Shell pane.

1. Ensure **PowerShell** is selected in the upper-left drop-down menu of the Cloud Shell pane.

1. In the PowerShell session within the Cloud Shell pane, run the following to create a variable storing the **Application (client) ID** you recorded in the previous task (replace the `<Azure_AD_Application_ID>` placeholder with the value of the **Application (client) ID**):
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
1. In the PowerShell session within the Cloud Shell pane, run the following to create a variable storing the Key Vault name.
	```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10-[DeploymentID]').VaultName

    $kvName
    ```
      
  >**Note**: In the above code snippet, replace the **-[DeploymentID]** with **<inject key="DeploymentID" enableCopy="true"/>**.

1. In the PowerShell session within the Cloud Shell pane, run the following to grant permissions on the Key Vault to the application you registered in the previous task:

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10-[DeploymentID] -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```
    
     >**Note**: In the above code snippet, replace the **[DeploymentID]** with **<inject key="DeploymentID" enableCopy="true"/>**.

1. Close the Cloud Shell pane. 

#### Task 3: Retrieve SQL Azure database ADO.NET Connection String 

The ARM-template deployment in Exercise 1 provisioned an Azure SQL Server instance and an Azure SQL database named **medical**. You will update the empty database resource with a new table structure and select data columns for encryption

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **SQL databases** and press the **Enter** key.

1. In the list of SQL databases, click the **medical(<randomsqlservername>)** entry.

    >**Note**: If the database cannot be found, this likely means the deployment you initiated in Exercise 1 has not completed yet. You can validate this by browsing to the Azure Resource Group **AZ500LAB10-<inject key="DeploymentID" enableCopy="false"/>** (or the name you chose), and selecting **Deployments** from the Settings pane.  

1. On the SQL database blade, in the **Settings** section, click **Connection strings**. 

    >**Note**: The interface includes connection strings for ADO.NET, JDBC, ODBC, PHP, and Go. 
   
1. Record the **ADO.NET Connection String**. You will need it later.

    >**Note**: When you use the connection string, make sure to replace the `{your_password}` placeholder with **Pa55w.rd1234**.

#### Task 4: Log on to the Azure VM running Visual Studio 2019 and SQL Management Studio 2019

In this task, you log on to the Azure VM, which deployment you initiated in Exercise 1. This Azure VM hosts Visual Studio 2019 and SQL Server Management Studio 2019.

>**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group **AZ500LAB10-<inject key="DeploymentID" enableCopy="false"/>**.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **virtual machines** and press the **Enter** key.

1. In the list of Virtual Machines shown, select the **az500-10-vm1** entry. On the **az500-10-vm1** blade, on the **Essentials** pane, take note of the **Public IP address**. You will use this later. 

#### Task 5: Create a table in the SQL Database and select data columns for encryption

In this task, you will connect to the SQL Database with SQL Server Management Studio and create a table. You will then encrypt two data columns using an autogenerated key from the Azure Key Vault. 

1. In the Azure portal, navigate to the blade of the **medical** SQL database, in the **Essentials** section, identify the server name, and then, in the toolbar, click **Set server firewall**.  

    >**Note**: Record the server name. You will need the server name later in this task.

1. On the **Firewall settings** blade, scroll down to **Rule Name**, click on **+ Add a firewall rule** and specify the following settings: 

    |Setting|Value|
    |---|---|
    |Rule Name|**Allow Mgmt VM**|
    |Start IP|the Public IP Address of the az500-10-vm1|
    |End IP|the Public IP Address of the az500-10-vm1|

1. Click **Save** to save the change and close the confirmation pane. 

    >**Note**: This modifies the server firewall settings, allowing connections to the medical database from the Azure VM's public IP address you deployed in this lab.

1. Navigate back to the **az500-10-vm1** blade, click **Overview**, next click **Connect** and, in the drop-down menu, click **RDP**. 

1. Click **Download RDP File** and use it to connect to the **az500-10-vm1** Azure VM via Remote Desktop. When prompted to authenticate, provide the following credentials and click **Ok**. 

    |Setting|Value|
    |---|---|
    |User name|**Student**|
    |Password|**Pa55w.rd1234**|
	
   In the pop that follows, click on **Yes**.

    >**Note**: Wait for the Remote Desktop session and **Server Manager** to load. Close Server Manager. 

    >**Note**: The remaining steps in this lab are performed within the Remote Desktop session to the **az500-10-vm1** Azure VM. 

1. Inside the Virtual Machine, Select the Windows Start button and type SSMS(1). Select Microsoft SQL Server Management Studio 19(2) from the list.

1. In the **Connect to Server** dialog box, specify the following settings: 

    |Setting|Value|
    |---|---|
    |Server Type|**Database Engine**|
    |Server Name|the server name you identified earlier in this task|
    |Authentication|**SQL Server Authentication**|
    |Login|**Student**|
    |Password|**Pa55w.rd1234**|

1. In the **Connect to Server** dialog box, click **Connect**.

1. Within the **SQL Server Management Studio** console, in the **Object Explorer** pane, expand the **Databases** folder.

1. In the **Object Explorer** pane, right-click the **medical** database and click **New Query**.

1. Paste the following code into the query window and click **Execute**. This will create a **Patients** table.

     ```sql
     CREATE TABLE [dbo].[Patients](
		[PatientId] [int] IDENTITY(1,1),
		[SSN] [char](11) NOT NULL,
		[FirstName] [nvarchar](50) NULL,
		[LastName] [nvarchar](50) NULL,
		[MiddleName] [nvarchar](50) NULL,
		[StreetAddress] [nvarchar](50) NULL,
		[City] [nvarchar](50) NULL,
		[ZipCode] [char](5) NULL,
		[State] [char](2) NULL,
		[BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
1. After the table is created successfully, in the **Object Explorer** pane, expand the **medical** database node. Now expand the **Tables** node and right-click the **dbo.Patients** node, and click **Encrypt Columns**. 

    >**Note**: This will initiate the **Always Encrypted** wizard.

1. On the **Introduction** page, click **Next**.

1. On the **Column Selection** page, select the **SSN** and **Birthdate** columns, set the **Encryption Type** of the **SSN** column to **Deterministic** and of the **Birthdate** column to **Randomized**, and click **Next**.

1. On the **Master Key Configuration** page, select **Azure Key Vault**, click **Sign in**. When prompted, authenticate by using the same user account you used to provision the Azure Key Vault instance earlier in this lab, ensure that Key Vault appears in the **Select an Azure Key Vault** drop down list, and click **Next**.

1. On the **Run Settings** page, click **Next**.
	
1. On the **Summary** page, click **Finish** to proceed with the encryption. When prompted, sign in again by using the same user account you used to provision the Azure Key Vault instance earlier in this lab.

1. Once the encryption process is complete, on the **Results** page, click **Close**.
	
    >**Note**: Process may take upto 5 minutes.

1. In the **SQL Server Management Studio** console, in the **Object Explorer** pane, under the **medical** node, expand the **Security** and **Always Encrypted Keys** subnodes. 

    >**Note**: The **Always Encrypted Keys** subnode contains the **Column Master Keys** and **Column Encryption Keys** subfolders.

### Exercise 4: Demonstrate the use of Azure Key Vault in encrypting the Azure SQL database

In this exercise, you will complete the following tasks:

#### Task 1: Run a data-driven application to demonstrate the use of Azure Key Vault in encrypting the Azure SQL database

You will create a Console application using Visual Studio to load data into the encrypted columns and then access that data securely using a connection string that accesses the key in the Key Vault.

1. From the RDP session to the **az500-10-vm1**, launch **Visual Studio 2019** from the **Start menu**.

2. Switch to the window displaying Visual Studio 2019 welcome message, click the **Sign in** button and, when prompted, provide the credentials you used to authenticate to the Azure subscription you are using in this lab. Now click **Start Visual studio**.

3. On the **Get started** page, click **Create a new project**. 

4. In the list of project templates, search for **Console App (.NET Framework)**, in the list of results, click **Console App (.NET Framework)** for **C#**, and click **Next**.

5. On the **Configure your new project** page, specify the following settings (leave other settings with their default values) and click on **create**

    |Setting|Value|
    |---|---|
    |Project name|**OpsEncrypt**|
    |Solution name|**OpsEncrypt**|
    |Framework|**.NET Framework 4.7.2.**|
6. In the Visual Studio console, click the **Tools** menu, in the drop down menu, click **NuGet Package Manager**, and, in the cascading menu, click **Package Manager Console**.

7. In the **Package Manager Console** pane, run the following to install the first required **NuGet** package:

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. In the **Package Manager Console** pane, run the following to install the second required **NuGet** package:

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
	
9. Minimize the RDP session to your Azure virtual machine, then navigate to **C:\AllFiles\AZ500-AzureSecurityTechnologies-prod\Allfiles\Labs\10\program.cs**, open it in Notepad, and copy its content into Clipboard.

10. Return to the RDP session, and in the Visual Studio console, in the **Solution Explorer** window, click **Program.cs** and replace its content with the code you copied into Clipboard.
	
11. In the Visual Studio window, in the **Program.cs** pane, in line 15, replace the `<connection string noted earlier>` placeholder with the Azure SQL database **ADO.NET** connection string you recorded earlier in the lab. In the connection string, replace the `{your_password}` placehodler, with `Pa55w.rd1234`. If you saved the string on the lab computer, you may need to leave the RDP session to copy the ADO string, then return to the Azure virtual machine to paste it in.

12. In the Visual Studio window, in the **Program.cs** pane, in line 16, replace the `<client id noted earlier>` placeholder with the value of **Application (client) ID** of the registered app you recorded earlier in the lab. 

13. In the Visual Studio window, in the **Program.cs** pane, in line 17, replace the `<key value noted earlier>` placeholder with the value of **Key1** of the registered app you recorded earlier in the lab. 

14. In the Visual Studio console, click the **Start** button to initiate the build of the console application and start it.

15. The application will start a Command Prompt window. When prompted for password, type **Pa55w.rd1234** to connect to Azure SQL Database. 

16. Leave the console app running and switch to the **SQL Management Studio** console. 

17. In the **Object Explorer** pane, right-click the **medical** database and, in the right-click menu, click **New Query**.

18. From the query window, run the following query by clicking on **Execute** to verify that the data that loaded into the database from the console app is encrypted.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. Switch back to the console application where you are prompted to enter a valid SSN. This will query the encrypted column for the data. At the Command Prompt, type the following and press the Enter key:

    ```cmd
    999-99-0003
    ```

    >**Note**: Verify that the data returned by the query is not encrypted.

20. To terminate the console app, press the Enter key

    
> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- Click the Lab Validation tab located at the upper right corner of the lab guide section and navigate to the Lab Validation Page.
- Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Review

In this lab, you have:

- Deployed the base infrastructure from an ARM template.
- Configured the Key Vault resource with a key and a secret.
- Configured an Azure SQL database and a data-driven application.
- Demonstrated the use of Azure Key Vault in encrypting the Azure SQL database.

## You have successfully completed the lab
