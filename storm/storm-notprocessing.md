## Storm Topology Not Processing Messages now. Was working before.

### Issue

Customer reports that their storm topology is not processing any data from Kafka. It was working before.
Customer reports that the Topologies going stale after few hours of processing.

### Description

There could be several reasons why the topology is not processing messages. 
From the incidents that we have seen with Storm topologies, the issue usually hasn't been with the Apache Storm project itself, but with the customer code issues / excessive logging in some cases.
There are multiple bolts and spouts which form a storm processing pipeline. Bottleneck in any one of them can cause issues in the whole pipeline not being able to process messages.
Before you open a support ticket please collect the DEBUG logs when the issue is happening.

### What to check:
1. *Storm UI* and navaigate to the topology that is not processing messages under Topology Summary
https://<clustername>.azurehdinsight.net/stormui

2. Topology Stats 10m 0s window

2. Bolts (All time) *Capacity* - In Storm UI for topology that is affected check at column *Capacity* for bolts. Any value greater than or close to 1 is an issue. A capacity of 1 means that we are executing at the limit for that particular bolt.
There are multiple bolts and spouts which form a storm processing pipeline. Bottleneck in any one of them can cause issues in the whole pipeline not being able to process messages.

If the Capacity is higher than 1
  - Ask the customer to look at code optimizations to reduce capacity
  - Suggest increasing parallism and having more executors for the problematic bolt.

3. *Kafka Spouts Lag*
  - Check the column *Lag* from the Kafka Spout. A value of 0 is what we desire over here
  - Right after redeploy of topology there might be a small amount of time when the lag is not zero when Storm is processing messages from Kafka from the last time it was stopped. Its expected to take sometime to catch up.
 
4. How to get DEBUG Logs.
  Before you open a support ticket please make sure you have collected atleast 1 minute worth of DEBUG logs from the worker nodes where the problem might be happening.
  Here are the steps to turn DEBUG logging ON and OFF.
  
  Please replace <version>  [topology name] [logger name] [LEVEL] and [TIMEOUT] with real values corresponding to customers cluster.
  cd /usr/hdp/ <version> /storm
  ./bin/storm set_log_level [topology name]-l [logger name]=[LEVEL]:[TIMEOUT]  
  
  Example:
  cd /usr/hdp/2.6.2.2-5/storm
  ./bin/storm set_log_level cy17-binary-decoder -l ROOT=DEBUG:30
  ./bin/storm set_log_level cy17-binary-decoder -l ROOT=INFO:30
  
  
  