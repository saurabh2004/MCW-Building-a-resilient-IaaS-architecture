![](images/HeaderPic.png "Microsoft Cloud Workshops")

# Building a resilient IaaS architecture

## Hands-on lab unguided

## March 2018

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.
© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

[Building a resilient IaaS architecture hands-on lab unguided](#building-a-resilient-iaas-architecture-hands-on-lab-step-by-step)
-   [Abstract and learning objectives](#abstract-and-learning-objectives)
-   [Overview](#overview)
-   [Requirements](#requirements)
-   [Solution Architecture](#solution-architecture)
-   [Help References](#help-references)
-   [Before the hands-on lab ](#before-the-hands-on-lab)
    -   [Task 1: Create a Virtual Machine using the Azure portal](#task-1-create-a-virtual-machine-using-the-azure-portal)
    -   [Task 2: Connect to the VM and download the student files](#_Toc505347855)
    -   [Task 3: Update the Azure PowerShell CmdLets](#_Toc505347856)
    -   [Task 4: Validate Connectivity to Azure](#_Toc505347857)
    -   [Task 5: Create a Storage Account for Artifact Storage](#_Toc505347858)
    -   [Task 6: Run Script to create the Active Directory deployment](#_Toc505347859)
    -   [Task 7: Run Script to create the Web and SQL Tiers](#task-3-deploy-the-lab-environment)
-   [Exercise 1: Prepare the Infrastructure Region 2](#exercise-1-prepare-connectivity-between-regions)
    -   [Task 1: Create a Virtual Network for the Azure Infrastructure (Region 2)](#_Toc505347863)
    -   [Task 2: Add Gateway subnet to the VNET (Region 2)](#_Toc505347864)
    -   [Task 3: Deploy VPN Gateway (Region 2)](#_Toc505347865)
    -   [Task 4: Create a Backup Vault (Region 2)](#_Toc505347866)
-   [Exercise 2: Resilient Infrastructure Options Region 1](#_Toc505347868)
    -   [Task 1: Add Gateway subnet to existing VNET (Region 1)](#_Toc505347869)
    -   [Task 2: Deploy VPN Gateway (Region 1)](#_Toc505347870)
    -   [Task 3: Create a Backup Vault (Region 1)](#_Toc505347871)
    -   [Task 4: Modify load balancer Settings (Region 1)](#_Toc505347872)
-   [Exercise 3: Build the DCs in for resiliency](#exercise-2-build-the-dcs-in-for-resiliency)
    -   [Task 1: Create Resilient Active Directory Deployment (Region 1)](#task-1-create-resilient-active-directory-deployment)
    -   [Task 2: Create the Active Directory Deployment (Region)](#task-2-create-the-active-directory-deployment-in-the-second-region)
    -   [Task 3: Add data disks to Active Directory domain controllers (both regions)](#_Toc504475398)
    -   [Task 4: Build a connection between the VPN Gateways](#_Toc505347878)
    -   [Task 5: Format data disks on DCs and configure DNS settings across connection](#task-4-format-data-disks-on-dcs-and-configure-dns-settings-across-connection)
    -   [Task 6: Promote DCs as additional domain controllers (both regions)](#task-5-promote-dcs-as-additional-domain-controllers)
-   [Exercise 4: Build web tier and SQL for resiliency](#_Toc505347882)
    -   [Task 1: Deploy SQL Always-On Cluster (Region 1)](#task-1-deploy-sql-always-on-cluster)
    -   [Task 2: Run Script to Deploy Web Tier Scale Set (Region 1)](#task-3-build-a-scalable-and-resilient-web-tier)
    -   [Task 3: Deploy SQL Always-On Cluster (Region 2)](#_Toc505347885)
    -   [Task 4: Deploy Web Tier Scale Set (Region 2)](#_Toc505347886)
-   [Exercise 5: Prepare other resources for resiliency](#_Toc505347888)
    -   [Task 1: Create Traffic Manager in Priority Mode](#_Toc505347889)
    -   [Task 2: Configure Operations Management Suite for Monitoring (Region 1 and 2)](#_Toc505347890)
    -   [Task 3: Configure Backups of IaaS Servers in Vaults (Region 1 and 2)](#_Toc505347891)
    -   [Task 4: Configure Network Security Groups as Needed (Region 1 and 2)](#_Toc505347892)
-   [After the hands-on lab](#_Toc505347894)
    -   [Task 1: Delete the resource groups created](#task-1-delete-the-resource-groups-created)

## Overview

Litware has asked you to deploy their infrastructure in a resilient manner to insure their infrastructure will be available for their users and gain an SLA from Microsoft.

## Solution architecture

Highly resilient deployment of Active Directory Domain Controllers in Azure.
    ![Highly resilient deployment of Active Directory Domain Controllers in Azure.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image2.png "Solution architecture")

Deployment of a web app using scale sets, and a highly available SQL Always On deployment.
    ![Deployment of a web app using scale sets, and a highly available SQL Always On deployment.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image3.png "Solution architecture")

## Requirements

1.  Microsoft Azure Subscription

2.  Virtual Machine Built during this hands-on lab or local machine with the following:

    -   Visual Studio 2017 Community or Enterprise Edition

    -   Latest Azure PowerShell Cmdlets

    -   <https://azure.microsoft.com/en-us/downloads/>

    -   Ensure you reboot after installing the SDK or Azure PowerShell will not work correctly

#### Help References
|    |            |
|----------|:-------------:|
| **Description** | **Links** |
| Authoring ARM Templates | <https://azure.microsoft.com/en-us/documentation/articles/resource-group-authoring-templates/> |
| Virtual Machine Scale Set Samples | <https://github.com/gbowerman/azure-myriad> |
| Azure Quick Start Templates | <https://github.com/Azure/azure-quickstart-templates> |
| Network Security Groups | <https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/> |
| Managed Disks | <https://azure.microsoft.com/en-us/services/managed-disks> |
| Always-On Availability Groups | <https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017> |
| SQL Server Managed Backup to Azure | <https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017> |
| Virtual Network Peering | <https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview> |
| Azure Backup |  <https://azure.microsoft.com/en-us/services/backup/> |

## Before the hands-on lab

Duration: 30 minutes

In this exercise, you build a Lab VM followed by preparing an Azure infrastructure containing several issues needing to be addressed from a resiliency standpoint. You will create an Active Directory environment, a SQL database tier, and a web tier for a Web Application.

#### Task 1: Create a Virtual Machine using the Azure portal 

1.  Launch a browser and navigate to <https://portal.azure.com>. Once prompted, login with your Microsoft Azure credentials. If prompted, choose whether your account is an organization account or just a Microsoft Account.

**Note**: You may need to launch an \"in-private\" session in your browser if you have multiple Microsoft Accounts.

2.  Click on **+NEW**, and in the search box, type in **Visual Studio Community 2017 on Windows Server 2016 (x64)** and press Enter. Click the Visual Studio Community 2017 image running on Windows Server 2016 and with the latest update.

3.  In the returned search results, click the image name.

    ![In the Azure Portal, in the Everything blade, the Search field is set to Visual Studio Community 2017 on Windows Server 2016 (x64). In the Results section, Visual Studio Community 2017 on Windows Server 2016 (x64) is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image4.png "Azure Portal, Everything blade")

4.  At the bottom of the page in the Marketplace solution blade, keep the deployment model set to **Resource Manager**, and click **Create**.

    ![In the Marketplace Solution blade, from the Select a deployment model drop-down menu, Resource Manager displays.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image5.png "Marketplace Solution blade")

5.  Set the following configuration on the Basics tab, and click **OK**.

    -   Name: **LABVM**

    -   VM disk type: **SSD**

    -   User name: **demouser**

    -   Password: **demo\@pass123**

    -   Subscription: **If you have multiple subscriptions choose the subscription to execute your labs in.**

    -   Resource Group: **OPSLABRG**

    -   Location: **Choose the closest Azure region to you.**

    ![Fields in the Basics blade display the previously defined settings.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image6.png "Basics blade")

6.  Choose the **DS1\_V2 Standard** instance size on the Size blade.

**Note**: You may have to click the View All link to see the instance sizes.

![In the Choose a size blade, DS1\_V2 Standard is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image7.png "Choose a size blade")

**Note**: If the Azure Subscription you are using is [NOT]{.underline} a trial Azure subscription, you may want to choose the DS2\_V2 to have more power in this LABMV. If you are using a trial subscription or one that you know has a restriction on the number of cores, stick with the DS1\_V2.

7.  Click **Configure required settings** to specify a storage account for your virtual machine if a storage account name is not automatically selected for you.

    ![In the Settings blade, Under Storage, Use managed disks is set to No, and Storage account is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image8.png "Settings blade")

8.  Click **Create new**.

    ![Screenshot of the Create new button.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image9.png "Create new button")

9.  Specify a unique name for the storage account (all lower letters and alphanumeric characters), and ensure the green checkmark showing the name is valid.

    ![The checkbox next to the Name field is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image10.png "Name field")

10. Click **OK** to continue.

    ![Screenshot of the OK button.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image11.png "OK button")

11. Click **Configure required settings** for the Diagnostics storage account if a storage account name is not automatically selected for you. Repeat the previous steps to select a unique storage account name. This storage account will hold diagnostic logs about your virtual machine that you can use for troubleshooting purposes.

    ![Screenshot of the Diagnostics storage account option.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image12.png "Diagnostics storage account")

12. Accept the remaining default values on the Settings blade, and click **OK**. On the Summary page, click **OK**. The deployment should begin provisioning. It may take more than 10 minutes for the virtual machine to complete provisioning.

    ![The Deploying Visual Studio Community 2017 notice displays.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image13.png "Deploying Visual Studio Community 2017")

**Note**: Once the deployment is complete, move on to the next exercise.

#### Task 2: Connect to the VM and download the student files

1.  Move back to the portal page on your local machine and wait for **LABVM** to show the Status of **Running**. Click **Connect** to establish a new remote desktop session.

    ![On the Azure Portal menu bar, Connect is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image14.png "Azure Portal")

2.  Depending on your remote desktop protocol client and browser configuration, you will either be prompted to open an RDP file, or you will need to download it followed by opening it up separately to connect. You may also be required to click, **Use a different account**.

    ![In the Windows Security dialog box, Use a different account is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image15.png "Windows Security dialog box")

3.  Login with the credentials specified during creation:

    -   User: **demouser**

    -   Password: **demo\@pass123**

4.  You will be presented with a remote desktop connection warning because of a certificate trust issue. Click, **Don't ask me again for connections to this computer** followed by **Yes** to continue with the connection.

    ![The Remote Desktop Connection warning window displays with the message that the identity of the remote computer cannot be verified, and asking if you want to continue anyway. the Don\'t ask me again for connections to the computer checkbox is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image16.png "Remote Desktop Connection warning window")

5.  When logging on for the first time, you will see a prompt on the right asking about network discovery. Click **No**.

    ![On the Networks page, the No button is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image17.png "Networks page")

6.  Notice that Server Manager opens by default. On the left, click **Local Server**.

    ![Local Server is selected in the Server Manager menu.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image18.png "Server Manager menu")

7.  On the right side of the pane, click **On** by **IE Enhanced Security Configuration**.

    ![In the Essentials section, IE Enhanced Security Configuration is set to On, and is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image19.png "Essentials section")

8.  Change to **Off** for Administrators and click **OK**.

    ![Administrators is set to Off in the IE Enhanced Security Configuration dialog box.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image20.png "IE Enhanced Security Configuration dialog box")

9.  In the lower left corner, click Internet Explorer to open it. On first use, you will be prompted about security settings. Accept the defaults by clicking **OK**.

    ![In the Internet Explorer 11 dialog box, the option to Use recommended security, privacy, and compatibility settings is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image21.png "Internet Explorer 11 dialog box")

10. If prompted, click **Don't show this again** regarding protected mode.

11. To download the exercise files for the hands-on lab, paste this URL into the browser.

    https://cloudworkshop.blob.core.windows.net/resilient-iaas-hackathon/Building_Resilient_Iaas_Hackathon_Student_Files.zip  

12. You will be prompted about what you want to do with the file. Select **Save**.

    ![In the Internet Explorer dialog box, the Save button is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image22.png "Internet Explorer dialog box")

13. Download progress is shown at the bottom of the browser window. When the download is complete, click **Open folder**.

    ![On the Download Complete bar, the Open folder button is selected.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image23.png "Download Complete bar")

14. The **Downloads** folder will open, ***Right-click*** the zip file, and click **Extract All**. In the **Extract Compressed (Zipped) Folders** window, enter **C:\\HOL** in the **Files will be extracted to this folder** dialog. Click the **Extract** button.

#### Task 3: Deploy the Lab Environment

1.  Login to the Azure portal (<https://portal.azure.com>) with the credentials that you want to deploy the lab environment to.

2.  In a separate tab, navigate to: <https://github.com/opsgility/cw-building-resilient-iaas-architecture>.

3.  Click the button **Deploy to Azure**.

    ![A screen with the Deploy to Azure button visible.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image24.png "Sample Application in GitHub")

4.  Specify the Resource group name as **LitwareRG** and the region as **West Central US**, **check the two checkboxes** on the page and click **Purchase**.

    ![The custom deployment screen with LitwareRG as the resource group and West Central US as the region.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image25.png "Custom deployment")

5.  Once the deployment is successful, validate the deployment by opening the **CloudShopWeb** virtual machine and navigating your browser to its public IP address.

    ![The CloudShopWeb blade in the Azure portal with the public IP address highlighted.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image26.png "CloudShopWeb ")

    ![The CloudShopDemo window displays. Under Select a product from the list, a product list displays.](images/Hands-onlabunguided-BuildingaresilientIaaSarchitectureimages/media/image27.png "CloudShopDemo window")

#### Summary

In this exercise, you prepared an Azure infrastructure containing several issues needing to be addressed from a resiliency standpoint. You created an Active Directory environment, a SQL Database tier, and a web tier for a Web Application.

You should follow all steps provided *before* attending the Hands-on lab.

## Exercise 1: Prepare connectivity between regions

Duration: 30 minutes

Litware is planning to deploy infrastructure in multiple regions in Azure to provide infrastructure closer to their employees in each region as well as the ability to provide additional resiliency in the future for certain workloads. In this exercise, you will configure connectivity between the two regions.

#### Task 1: Create a VNET in the second region

Tasks to complete

-   Create a new Virtual network in the West US 2 region named LitwareVNET2 that mirrors LitwareVNET except with a different address space.

Exit criteria

-   There should be a new virtual network with two subnets: Apps and Data in the West US 2 region.

## Exercise 2: Build the DCs in for resiliency

Duration: 30 minutes

In this exercise, you will deploy Windows Server Active Directory configured for resiliency using Azure Managed Disks and Availability Sets in the primary region. You will then deploy additional domain controllers in a second region for future expansion of Litware's Azure footprint.

#### Task 1: Create Resilient Active Directory Deployment 

Tasks to complete

-   Create two Domain Controllers (DCs) in the first region: LitwareDC01, LitwareDC02.

Exit criteria

-   The DCs should be configured for resiliency in availability sets and with managed disks.

-   The DCs should also be configured for Azure Backup.

#### Task 2: Create the Active Directory deployment in the second region

Tasks to complete

-   Create two Domain Controllers (DCs) in the second region: LitwareDC03, LitwareDC04.

Exit criteria

-   The DCs should be configured for resiliency in availability sets and with managed disks.

-   The DCs should also be configured for Azure Backup.

#### Task 3: Add data disks to Active Directory domain controllers (both regions)

Tasks to complete

-   Add an additional data disk (managed) to each of the domain controllers

Exit criteria

-   Each DC should have an additional SSD based 1023 GB managed disk attached.

#### Task 4: Format data disks on DCs and configure DNS settings across connection

Tasks to complete

-   Format the disks as the F: drive on each of the VMs

-   Configure the virtual networks in each region to reference the IPs of the new Domain Controllers.

-   Run the following script on the ADVM virtual machine:

-   Set-DnsServerPrimaryZone -Name Litware.com -DynamicUpdate NonsecureAndSecure ??

Exit criteria

-   Each DC should have an additional SSD based 1023 GB managed disk attached and formatted.

-   The Virtual Networks in each region should reference the local DCs in each region.

-   The Set-DnsServerPrimary zone cmdlet should be executed on ADVM.

#### Task 5: Promote DCs as additional domain controllers 

Tasks to complete

-   Promote the four DCs to join the litware.com Active Directory domain.

Exit criteria

-   All the DCs should be Domain Controllers.

#### Summary

In this exercise, you will deploy Windows Server Active Directory configured for resiliency using Azure Managed Disks and Availability Sets in the primary and the failover region.

## Exercise 3: Build web tier and SQL for resiliency

Duration: 60 minutes

In this exercise, you will deploy resilient web servers using VM scale sets and a SQL Always-On Cluster for resiliency at the data tier.

#### Task 1: Deploy SQL Always-On Cluster 

In this task, you will deploy a SQL Always-On cluster using an ARM template that deploys to your existing Virtual Network and Active Directory infrastructure.

Tasks to complete

1.  Navigate to <https://github.com/opsgility/cw-building-resilient-iaas-architecture-sql> and click the **Deploy to Azure Button**. Deploy the template to the LitwareCloudShopRG resource group in the West Central US region.

2.  After the template is deployed, execute the following command on SQLVM-1:
    ```
    New-Cluster -Name CLUST-1 -Node SQLVM-1,SQLVM-2,WITNESSVM -StaticAddress 10.0.1.8 
    ```

3.  Enable SQL Server AlwaysOn on SQLVM-1 and SQL VM-2 and change the service login for both to litware\\demouser.

4.  Copy the script from: **C:\\HOL\\CreateSQLAG.sql** on the LABVM to C:\\SQATA on SQLVM-1**.** Execute the script in cmd mode.

Exit criteria

-   SQL AlwaysOn Availability groups should be deployed.

#### Task 2: Convert the SQL deployment to Managed Disks 

In this task, you will convert the disks of the SQL deployment to managed disks. This task could be automated as part of the template deployment; however, it is important to understand how to migrate existing infrastructure to managed disks.

Tasks to complete

-   Execute the following script to convert the SQL disks to managed
    ```
    \<\#
    ```

    The following code converts the existing availability to aligned/managed and then converts the disks to managed as well. 
    
**Note**: the PlatformFaultDomainCount is set to 2 - this is because the region currently only supports two managed fault domains
```
    \#\>

    \$rgName = \'LitwareCloudShopRG\'

    \$avSetName = \'SQLAVSet\'

    \$avSet = Get-AzureRmAvailabilitySet -ResourceGroupName \$rgName -Name \$avSetName

    \$avSet.PlatformFaultDomainCount = 2

    Update-AzureRmAvailabilitySet -AvailabilitySet \$avSet -Sku Aligned

    foreach(\$vmInfo in \$avSet.VirtualMachinesReferences)

    {

    \$vm = Get-AzureRmVM -ResourceGroupName \$rgName \| Where-Object {\$\_.Id -eq \$vmInfo.id}

    Stop-AzureRmVM -ResourceGroupName \$rgName -Name \$vm.Name -Force

    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName \$rgName -VMName \$vm.Name

    }
```

Exit criteria

-   All the disks for the SQL deployment should be managed.

#### Task 3: Build a scalable and resilient web tier

In this task, you will deploy a VM scale set that can automatically scale up or down based on the CPU criteria. The application the scale set deploys points to the new SQL AlwaysOn availability group created previously.

Tasks to complete

-   Navigate to <https://github.com/opsgility/cw-building-resilient-iaas-architecture-ss> and click the **Deploy to Azure Button**. Deploy the template to the LitwareCloudShopRG resource group in the West Central US region.

Exit criteria

-   The scale set should be deployed, and you should be able to browse the CloudShop application from the public IP address assigned to the load balancer.

#### Summary

In this exercise, you deployed resilient web servers behind a load balancer, and a SQL Always-On Availability Group for database resiliency.

## Exercise 4: Configure SQL Server Managed Backup 

Duration: 15 minutes

In this exercise, you will configure SQL Server Managed Backup to back up to an Azure Storage Account.

#### Task 1: Create an Azure Storage Account

Tasks to complete

-   Create a storage account for SQL server backup data by executing the following PowerShell script on your LABVM.
    ```    
    $storageAcctName = "[unique storage account name]"

    $resourceGroupName = "LitwareCloudShopRG"
    $containerName= "backups"
    $location = "West Central US"
    $storageSkuName = "Standard_LRS"


    "Creating Storage Account $storageAcctName"
    $sa = New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName  `
                                    -Name $storageAcctName `
                                    -Location $location `
                                    -SkuName $storageSkuName 


    $storageKey = (Get-AzureRmStorageAccountKey -Name $storageAcctName -ResourceGroupName $resourceGroupName )[0].Value
    $context = New-AzureStorageContext -StorageAccountName $storageAcctName -StorageAccountKey $storageKey


    Write-Host "Creating New Storage Container  $containerName" 
    New-AzureStorageContainer -name $containerName -permission container -context $context


    $fullSasToken = New-AzureStorageContainerSASToken -Name $containerName -Permission rwdl -FullUri -Context $context  
    $containerUrl = $fullSasToken.Substring(0,$fullSasToken.IndexOf("?"))
    $sasToken = $fullSasToken.Substring($fullSasToken.IndexOf("?")+1)


    $enableManagedBackupScript = @"
    --------------------
    ---BEGIN TSQL Script
    --------------------
    CREATE CREDENTIAL [$containerUrl] 
    WITH IDENTITY = 'Shared Access Signature', 
         SECRET = '$sasToken' 

    GO

    EXEC msdb.managed_backup.sp_backup_config_basic   
     @enable_backup = 1,   
     @database_name = 'AdventureWorks',  
     @container_url = '$containerUrl',   
     @retention_days = 30
       
     --------------------
     ---END TSQL Script
     --------------------
    "@


    write-host $enableManagedBackupScript 
    ```

-   Copy the generated tSQL code to notepad for later use.

Exit criteria

-   A storage account for SQL Server managed back and code to create an identity in SQL Server should be ready.

#### Task 2: Configure managed backup in SQL Server

Tasks to complete

-   Execute the following tSQL code on SQLVM-1 to enable the SQL Agent.
    ```
    EXEC sp_configure 'show advanced options', 1
    GO
    RECONFIGURE
    GO
    EXEC sp_configure 'Agent XPs', 1
    GO
    RECONFIGURE
    GO
    ```

-   Execute the code from the previous task that was copied to notepad on SQLVM-1.

-   Execute the following code to create a custom backup schedule.
    ```
    USE msdb;  
    GO  
    EXEC managed_backup.sp_backup_config_schedule   
         @database_name =  'AdventureWorks'  
        ,@scheduling_option = 'Custom'  
        ,@full_backup_freq_type = 'Weekly'  
        ,@days_of_week = 'Monday'  
        ,@backup_begin_time =  '17:30'  
        ,@backup_duration = '02:00'  
        ,@log_backup_freq = '00:05'  
    GO  
    ```
-   Execute the following code to create a backup immediately.
    ```
    EXEC msdb.managed_backup.sp_backup_on_demand   
    @database_name  = 'AdventureWorks',
    @type ='Database' 
    ```

Exit criteria

-   SQL Server should be configured to backup to an Azure Storage account based on your custom schedule.

-   SQL Server backup data should in the backups container of the Azure Storage Account

## Exercise 5: Validate resiliency

#### Task 1: Validate resiliency for the CloudShop application 

Tasks to complete

-   Spike the CPU of the Cloud Shop application by clicking the CPU spike button on the web apps home page.

Exit criteria

-   After 15-20 minutes new instances should spin up automatically from the auto scale rules.

#### Task 2: Validate SQL Always On

Tasks to complete

-   Within the Azure portal, click on Virtual Machines and open **SQLVM-1.** Click **Stop** at the top of the blade to shut the virtual machine off.

Exit criteria

-   **The SQL AlwaysOn cluster should automatically failover to SQLVM-2.**

#### Task 3: Validate backups are taken 

Tasks to complete

-   Open the Azure Backup Vaults created earlier and ensure that backup data for the VMs is present.

-   Open the container for the SQL Server backup storage account and ensure backup data is present.

Exit criteria

## After the hands-on lab 

Duration: 10 minutes

#### Task 1: Delete the resource groups created

-   Within the Azure portal, click Resource Groups on the left navigation.

-   Delete each of the resource groups created in this lab by clicking them followed by clicking the Delete Resource Group button. You will need to confirm the name of the resource group to delete.

You should follow all steps provided *after* attending the hands-on lab.
