<properties
   pageTitle="Use Hadoop Hive in HDInsight | Azure"
   description="Learn how to use Hive with the web-based HDInsight query console."
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

# Run Hive queries using the Query Console

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In this article, you will learn how to use the HDInsight Query Console to run Hive queries on an HDInsight Hadoop cluster from your browser.

> [AZURE.NOTE] The Query Console is only available on Windows-based HDInsight clusters

##<a id="prereq"></a>Prerequisites

To complete the steps in this article, you will need the following.

* A Windows-based HDInsight Hadoop cluster

* A modern web browser

##<a id="run"></a> Run Hive queries using the Query Console

1. Open the <a href="https://manage.windowsazure.com" target="_blank">Azure Management Portal</a> and select your HDInsight cluster. From the bottom of the page, select **Query Console**. When prompted, enter the username and password you used when creating the cluster.

    > [AZURE.NOTE] You can also reach the Query Console by entering **https://CLUSTERNAME.azurehdinsight.net** in your browser.

2. From the links at the top of the page, select **Hive Editor**. This displays a form that can be used to enter HiveQL statements that you want to run on the HDInsight cluster. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	Replace the text `Select * from hivesampletable` with the following HiveQL statements.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    These statements perform the following actions

    * **DROP TABLE** - deletes the table and the data file, in case the table already exists
    * **CREATE EXTERNAL TABLE** - creates a new 'external' table in Hive. External tables only store the table definition in Hive - the data is left in the original location

    > [AZURE.NOTE] External tables should be used when you expect the underlying data to be updated by an external source, such as an automated data upload process, or by another MapReduce operation, but always want Hive queries to use the latest data.
    >
    > Dropping an external table does **not** delete the data, only the table definition.

    * **ROW FORMAT** - tells Hive how the data is formatted. In this case, the fields in each log are separated by a space
    * **STORED AS TEXTFILE LOCATION** - tells Hive where the data is stored (the example/data directory,) and that it is stored as text
    * **SELECT** - select a count of all rows where column **t4** contain the value **[ERROR]**. This should return a value of **3** as there are three rows that contain this value

2. Select **Submit**. The **Job Session** at the bottom of the page should display details for the job.

3. Once the **Status** field changes to **Completed**, select **View Details** for the job. On the details page, the **Job Output** will contain `[ERROR]	3`. You can use the **Download** button below this field to download a file containing the output of the job.


##<a id="summary"></a>Summary

As you can see, the Query Console provides an easy way to run Hive queries on an HDInsight cluster, monitor the job status, and retrieve the output. 

To learn more about Hive using the Query Console, select **Getting Started** from the top of the Query Console, then use the samples. Each sample walks through the process of analyzing data using Hive, including explainations of the HiveQL statements used in the sample.

##<a id="nextsteps"></a>Next steps

For general information on Hive in HDInsight.

* [Use Hive with Hadoop on HDInsight](hdinsight-use-hive.md)

For information on other ways you can work with Hadoop on HDInsight.

* [Use Pig with Hadoop on HDInsight](hdinsight-use-pig.md)

* [Use MapReduce with Hadoop on HDInsight](hdinsight-use-mapreduce.md)

[1]: hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
