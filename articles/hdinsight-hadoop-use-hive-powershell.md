<properties
   pageTitle="Use Hadoop Hive in HDInsight | Azure"
   description="Use Hadoop Hive in HDInsight through PowerShell."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Run Hive queries using PowerShell

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

This document provides an example of using PowerShell to run Hive queries on a Hadoop on HDInsight cluster.

> [AZURE.NOTE] This document does not provide a detailed description of what the HiveQL statements used in the examples do. For information on the HiveQL used in this example, see <a href="hdinsight-use-hive.md" target="_blank">Use Hive with Hadoop on HDInsight</a>.


##<a id="prereq"></a>Prerequisites

To complete the steps in this article, you will need the following.

* An Azure HDInsight (Hadoop on HDInsight) cluster (either Windows or Linux-based)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>Run Hive queries using PowerShell

Azure PowerShell provides *cmdlets* that allow you to remotely run Hive queries on HDInsight. Internally, this is accomplished by using REST calls to <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (formerly called Templeton,) running on the HDInsight cluster.

The following cmdlets are used when running Hive queries on a remote HDInsight cluster.

* **Add-AzureAccount** - Authenticates PowerShell to your Azure Subscription

* **New-AzureHDInsightHiveJobDefinition** - Creates a new *job definition* using the specified HiveQL statements

* **Start-AzureHDInsightJob** - Sends the job definition to HDInsight, starts the job, and returns a *job* object that can be used to check the status of the job

* **Wait-AzureHDInsightJob** - Uses the job object to check the status of the job. It will wait until the job has completed, or the wait time has been exceeded

* **Get-AzureHDInsightJobOutput** - Used to retrieve the output of the job

* **Invoke-Hive** - Used to run HiveQL statements and block it completes. Then, returns the results

* **Use-AzureHDInsightCluster** - Set the current cluster to use for  the **Invoke-Hive** command

The following steps demonstrate how to use these cmdlets to run a job on your HDInsight cluster. 

1. Using an editor, save the following code as **hivejob.ps1**. You must replace **CLUSTERNAME** with the name of your HDInsight cluster.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. Open a new **Microsoft Azure PowerShell** prompt. Change directories to the location of the **hivejob.ps1** file, then use the following to run the script.

		.\hivejob.ps1

7. Once the job completes, it should return information similar to the following.

		Display the standard output...
		[ERROR]	3

4. As mentioned earlier **Invoke-Hive** can be used to run a query and await on the response. Use the following commands, replacing **CLUSTERNAME** with the name of your cluster.

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	The output will look like the following.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] For longer HiveQL queries, you can use PowerShell Here-Strings or HiveQL script files. The following snippet shows how to use the *Invoke-Hive* cmdlet to run a HiveQL script file. The HiveQL script file must be uploaded to WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> For more information about Here-Strings, see <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Using Windows PowerShell Here-Strings</a>.

##<a id="troubleshooting"></a>Troubleshooting

If no information is returned when the job completes, an error may have occurred during processing. To view error information for this job, add the following to the end of the **hivejob.ps1** file, save it, and then run it again.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

This will return the information written to STDERR on the server when running the job, and may help determine why the job is failing.

##<a id="summary"></a>Summary

As you can see, Azure PowerShell provides an easy way to run Hive queries on an HDInsight cluster, monitor the job status, and retrieve the output.

##<a id="nextsteps"></a>Next steps

For general information on Hive in HDInsight.

* [Use Hive with Hadoop on HDInsight](../hdinsight-use-hive/)

For information on other ways you can work with Hadoop on HDInsight.

* [Use Pig with Hadoop on HDInsight](hdinsight-use-pig.md)

* [Use MapReduce with Hadoop on HDInsight](hdinsight-use-mapreduce.md)
