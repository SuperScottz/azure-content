<properties 
	pageTitle="Data Factory - Release Notes | Azure" 
	description="Data Factory release notes" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="spelluru"/>

# Azure Data Factory release notes

## Notes for 2/27/2015 release of Data Factory

### New improvements
- **Azure Data Factory Editor**. The Data Factory Editor, which is part of the Azure Preview Portal, allows you to create, edit, and deploy JSON files that define linked services, data sets, and pipelines.The main goal of the editor is to provide you a fast and light-weight user-interface (UI) to create Azure Data Factory artifacts without requiring you to install Azure PowerShell and ramp up on using PowerShell cmdlets. See the [Azure Data Factory Editor - A Light Weight Web Editor][adf-editor-blog] blog post for a quick overview and a video on Data Factory Editor. For detailed overview of the editor, see the [Data Factory Editor][adf-editor] article.          

### Changes

## Notes for 1/26/2015 release of Data Factory ##

### Changes
- Updated **Data Management Gateway** installation package has been posted to [Microsoft Download Center][adf-gateway-download]. Starting from this release, you can find the latest Data Management Gateway to use with Azure Data Factory at this download location. This installation package serves both Azure Data Factory and Power BI for Office 365 services. If you are using both the services, note that gateways for Data Factory and Power BI must be installed on different machines, and configured differently as per guidance from the Data Factory or Power BI documentation.
- The **Copy Activity** now supports copying data between on-premises SQL Server database and an Azure SQL database.  See [Copy Activity][adf-copy-activity] for details and [GitHub][adf-github-samples] for JSON samples.
- **SqlSink** supports a new property: **WriteBatchTimeout**. This property gives you the flexibility to configure how long to wait for the batch insert operation to complete before the operation times out. For a hybrid copy (copy operation that involves an on-premises data source and a cloud data source), you must have the gateway of version 1.4 or higher to use this property. 
- **SQL Server linked service** now supports **Windows Authentication**. 
	- When creating a SQL Server linked service using the portal, you can now choose to use Windows Authentication and set appropriate credentials. This requires you to have the gateway of version 1.4 or higher. 
	- When creating a SQL Server linked service using Azure PowerShell, you can specify connection information in plain text or encrypt the connection information using updated [New-AzureDataFactoryEncryptValue cmdlet][adf-encrypt-value-cmdlet] and then use the encrypted string for the Connection String property in the linked service JSON payload. See [Linked Services][adf-msdn-linked-services] for details about defining a linked service in JSON. The encryption feature is not supported by the New-AzureDataFactoryEncryptValue cmdlet yet. 

## Notes for 12/11/2014 release of Data Factory ##

### New improvements

- Azure Machine Learning integration
	- This release of Azure Data Factory service allows you to integrate Azure Data Factory with Azure Machine Learning (ML) by using **AzureMLLinkedService** and **AzureMLBatchScoringActivity**. See [Create predictive pipelines using Data Factory and Azure Machine Learning][adf-azure-ml] for details. 
- Gateway version status is provided
	- "NewVersionAvailable" status will be shown in the Azure Preview Portal and in the output of Get-AzureDataFactoryGateway cmdlet, if there is a newer version of the gateway available than the one that is currently installed. You can then follow the portal journey  to download the new installation file (.msi) and run it to install the latest gateway. There is no additional configuration is  needed.

### Changes

- JobsContainer in HdInsightOnDemandLinkedService is removed.
	- In the JSON definition for a HDInsightOnDemandLinkedService, you do not need to specify **jobsContainer** property anymore. If you have the property specified for an on-demand linked service, the property is ignored. You can remove the property from the JSON definition for the linked service and update the linked service definition by using New-AzureDataFactoryLinkedService cmdlet.
- Optional configuration parameters for HDInsightOnDemandLinkedService
	- This release introduces support for a few optional configuration parameters for HDInsightOnDemandLinked (on-demand HDInsight cluster). See [ClusterCreateParameters Properties][on-demand-hdi-parameters] for details.
- Gateway location is removed
	- When creating an Azure Data Factory gateway via portal or PowerShell (New-AzureDataFactoryGateway), you no longer need to specifiy the location for the gateway. The data factory region will be inherited. Similarly, to configure a SQL Server linked Service using JSON, "gatewayLocation" property is not needed anymore. Data Factory .NET SDK is also updated to refelct these changes.
	- If you use an older version of SDK and Azure PowerShell, you are still required to provide the location setting.
 
     

#### Breaking changes
	
- CustomActivity to DotNetActivity
	- **ICustomActivity** interface is renamed to **IDotNetActivity**. You will need to update Data Factory NuGet packages and change ICustomActivity to IDotNetActivity in the source code for your custom activity.  
	- The type of custom activity in the JSON definition for you custom activity must be changed from **CustomActivity** to **DotNetActivity**. 
	- The **CustomActivity** and **CustomActivityProperties** classes have been renamed to **DotNetActivity** and **DotNetActivityProperties** with the same set of properties.

		If you use the older verion of SDK and Azure PowerShell, you can continue using CustomActivity instead of DotNetActivity.
    
  		See [Use custom activities in an Azure Data Factory pipeline][adf-custom-activities] for a walkthrough on how to create a custom activity and use it in an Azure Data Factory pipeline.  

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



