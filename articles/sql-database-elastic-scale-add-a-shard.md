<properties 
	pageTitle="Adding a Shard To an Elastic Scale Application" 
	description="How to use Elastic Scale APIs to add new shards to a shard set." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="torsteng@microsoft.com"/>

# Adding a Shard To an Elastic Scale Application 


## To Add a Shard for a New Range or Key  

Applications often need to simply add new shards to handle data that is expected from new keys or key ranges, for a shard map that already exists. For example, an application sharded by Tenant ID may need to provision a new shard for a new tenant, or data sharded monthly may need a new shard provisioned before the start of each new month. 

If the new range of key values is not already part of an existing mapping, it is very simple to add the new shard and associate the new key or range to that shard. 

###Example:  Adding a Shard and its Range to an Existing Shard Map
In the sample below, a database named **sample_shard_2** and all necessary schema objects inside of it have been created to hold range [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


## To Add a Shard for an Empty Part of an Existing Range  

In some circumstances, you may have already mapped a range to a shard and partially filled it with data, but you now want upcoming data to be directed to a different shard. For example, you shard by day range and have already allocated 50 days to a shard, but on day 24, you want future data to land in a different shard. The Elastic Scale Preview [Split-Merge Service](sql-database-elastic-scale-overview-split-and-merge.md) can perform this operation, but if data movement is not necessary (for example, data for the range of days [25, 50), i.e., day 25 inclusive to 50 exclusive, does not yet exist) you can perform this entirely using the Shard Map Management APIs directly.

###Example:  Splitting a Range and Assigning the Empty Portion to a Newly-added Shard

A database named “sample_shard_2” and all necessary schema objects inside of it have been created.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Important**:  Use this technique only if you are certain that the range for the updated mapping is empty.  The methods above do not check data for the range being moved, so it is best to include checks in your code.  If rows exist in the range being moved, the actual data distribution will not match the updated shard map. Use the [Split-Merge Service](sql-database-elastic-scale-overview-split-and-merge.md) to perform the operation instead in these cases.  


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
