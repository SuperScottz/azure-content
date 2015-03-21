<properties 
	pageTitle="Azure Data Factory - Frequently Asked Questions" 
	description="Frequently asked questions about Azure Data Factory." 
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
	ms.date="03/03/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Frequently Asked Questions

## General questions

### Q: What is Azure Data Factory?

Data Factory is a fully managed service for developers to compose data storage, movement, and processing services into highly available, fault tolerant data pipelines. Data Factory operates over both on-premises and cloud data storage. A pipeline is a set of data inputs, processing activities, and data outputs and is defined with simple JSON scripting and activated via PowerShell commands. Once activated, Data Factory orchestrates and schedules pipelines to run on HDInsight (Hadoop) with options for automatic cluster management on behalf of the user. Data Factory also provides a visual management and monitoring experience through the Azure Preview Portal to monitor all pipelines with rich operational and service health information in one dashboard.
 
### Q: What customer challenge does Data Factory solve?

Azure Data Factory balances the agility of leveraging diverse data storage, processing and movement services across traditional relational storage alongside unstructured data, with the control and monitoring capabilities of a fully managed service.

### Q: Who are the target audiences for Data Factory?


- Data Developers: who are responsible for building integration services between Hadoop and other systems:
	- Must keep up and integrate with a continually changing and growing data landscape
	- Must write custom code for information production, and it  is expensive, hard to maintain, and not highly available or fault tolerant

- IT Professionals: who are looking to incorporate more diverse data within their IT infrastructure:
	- Required to look across all of an organization’s data to derive rich business insights
	- Must manage compute and storage resources to balance cost and scale across on-premises and cloud
	- Must quickly add diverse sources and processing to address new business needs, while maintaining visibility across all compute and storage assets

###  Q: Where can I find pricing details for Azure Data Factory?

See [Data Factory Pricing Details page][adf-pricing-details] for the pricing details for the Azure Data Factory.  

### Q. How do I get started with Azure Data Factory?

- For an overview of Azure Data Factory, see [Introduction to Azure Data Factory][adf-introduction].
- For a quick tutorial, see [Get started with Azure Data Factory][adfgetstarted].
- For comprehensive documentation, see [Azure Data Factory documentation][adf-documentation-landingpage].

  
### Q: How do customers access Data Factory?

Customers can get access to Data Factory through the [Azure Preview Portal][azure-preview-portal].

### Q: What is the Data Factory’s region availability?

At public preview, Data Factory will only be available in US West.  The compute and storage services used by data factories can be in other regions.
 
### Q: What are the limits on number of data factories/pipelines/activities/datasets? 


- Number of data factories within a subscription: 50
- Number of pipelines within a data factory: 100
- Number of activities within a pipeline: 10
- Number of datasets with in a data factory: 100

## Activities - FAQ
### Q: What are the supported data sources and activities?

- **Supported data sources:** Azure Storage (Blob and Tables), SQL Server, Azure SQL Database, File System. 
- **Supported activities:**: Copy Activity (on-premises to cloud, and cloud to on-premises), HDInsight Activity (Pig, Hive, and MapReduce transformations), Azure Machine Learning Batch Scoring Activity, Stored Procedure activity, and custom C# activities.

### When does an activity run?
The **availability** configuration setting in the output data table determines when the activity is run. The activity checks whether all the input data dependencies are satisfied (i.e., **Ready** state) before it starts running.

## Copy Activity - FAQ
### Q: What regions are supported by the Copy Activity ?

The Copy Activity supports copying data into the following regions: East US, East US 2, West US,  Central US, North Europe, West Europe, and South East Asia.

Copying data into other regions is also supported, by using one of the regions above for routing the data.  Copy operation is metered based on the region where data is routed through.

Region of copy destination | Region used for routing
-------------------------- | -----------------------
North Central US | East US 2
South Central US | West US
East Asia | South East Asia
Japan East | West US
Japan West | West US
Brazil South | East US 2

### How can I copy to multiple output tables ?
You can have multiple output tables in a pipeline as shown in the following example:

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### Is it better to have a pipeline with multiple activities or a separate pipeline for each activity? 
Pipelines are supposed to bundle related activities.  Logically, you can keep the activities in one pipeline if the tables that connect them are not consumed by any other activity outside the pipeline. This way, you would not need to chain pipeline active periods so that they align with each other. Also, the data integrity in the tables internal to the pipeline will be better preserved when updating the pipeline. Pipeline update essentially stops all the activities within the pipeline, removes them, and creates them again. From authoring perspective, it might also be easier to see the flow of data within the related activities in one JSON file for the pipeline. 

## HDInsight Activity - FAQ

### Q: What regions are supported by HDInsight?

See the Geographic Availability section in the following article: or [HDInsight Pricing Details][hdinsight-supported-regions].

### Q: What region is used by an on-demand HDInsight cluster?

The on-demand HDInsight cluster is created in the same region where the storage you specified to be used with the cluster exists.    

### Q: How to associate additional storage accounts to your HDInsight cluster?

If you are using your own HDInsight Cluster (BYOC - Bring Your Own Cluster), see the following topics: 

- [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores][hdinsight-alternate-storage]
- [Use Additional Storage Accounts with HDInsight Hive][hdinsight-alternate-storage-2]

If you are using an on-demand cluster that is created by the Data Factory service, you need to specify additional storage accounts for the HDInsight linked service so that the Data Factory service can register them on your behalf. In the JSON definition for the on-demand linked service, use **additionalLinkedServiceNames** property to specify alternate storage accounts as shown in the following JSON snippet:
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

In the example above, otherLinkedServiceName1 and otherLinkedServiceName2 represent linked services whose definitions contain credentials that the HDInsight cluster needs to access alternate storage accounts.

## Stored Procedure Activity - FAQ
### What data sources does the Stored Procedure Activity support?
The Stored Procedure Activity supports only Azure SQL Database at this time. 

## Slices - FAQ

### How can I rerun a slice?
You can rerun a slice in one of the following ways: 

- Click **Run** in the command bar on the **DATA SLICE** blade for the slice in the portal. 
- Run **Set-AzureDataFactorySliceStatus** cmdlet with Status set to **PendingExecution** for the slice.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

See [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] for details about the cmdlet. 

### How long did it take to process a slice?
1. Click **Datasets** tile on the **DATA FACTORY** blade for your data factory.
2. Click the specific dataset on the **Datasets** blade.
3. Select the slice that you are interested in from the **Recent slices** list on the **TABLE** blade.
4. Click the activity run from the **Activity Runs** list on the **DATA SLICE** blade. 
5. Click **Properties** tile on the **ACTIVITY RUN DETAILS** blade. 
6. You should see the **DURATION** field with a value. This is the time taken to process the slice.   

### How to stop a running slice?
If you need to stop the pipeline from executing, you can use [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) cmdlet. Currently, suspending the pipeline does not stop the slice executions that are in progress. Once the in-progress executions finish, no extra slice is picked up.

If you really want to stop all the executions immediately, the only way would be to delete the pipeline and create it again. If you choose to delete the pipeline, you do NOT need to delete tables and linked services used by the pipeline. 


## See Also
[Introduction to Azure Data Factory][adf-introduction]
[Get started with Azure Data Factory][adfgetstarted]
[Troubleshooting Guide for Data Factory][adf-troubleshoot]

[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
