---
description: for the Big Data Seminar course
---

# Big Data Related Paper

## Scaling Big Data Mining Infrastructure: The Twitter Experience

There’s a lot more to put together a big data product than a machine learning product. For either of the cases, the first process is always to collect the data, conduct exploratory data analysis to understand the data, visualization & statistical analysis/exploration as well as data analysis. Getting the data and figuring out the distribution is just the start. 

The challenge for data scientists at internet companies like twitter is the process of making everything scalable and schematic. For them, it’s not just data cleaning and machine learning models. Their pain point is scheduling a workflow or a pipeline: building a structure that works like a clock, the system itself routinely does all the job in a regular basis.

Yet that’s not the end. The product they are working on is constantly evolving. The features that twitter originally had probably are long gone by now, and the engineers are always adding new features or attempting to ramp up a new feature after a successful A/B testing. Then a new problem emerges: how to organize the data and the logs? how to retrieve all the information we need in order to analyze certain user behavior? If the data itself is hard to gather together, there’s nowhere for them to begin with. 

With much consideration, small companies might find the key-value pairs and the use of JSON to be enough in their scale of business, but for large companies with much messier data, this is merely scant. One of the many reasons is that the json files contain data of many types, which makes parsing and maintenance very hard for the engineers. The simple choice of delimiter, which sometimes a ; and other times , would become a complicated issue in here. 

The author brought up some ways to avoid the issues: The usage of HDFS benefits in the way that it carries the log data and processes part of it on the fly as it transports the data as well as the log to the Hadoop cluster. Tools like Apache Thrift or Avro makes the logging messages more structured and systematic. 

## MapReduce: Simplified Data Processing on Large Clusters

The MapReduce system has its edge over the other distributed systems that were present as the authors wrote it. On a higher level, the system works like a factory: a coordinator \(master\) distributes the jobs for its subordinate workers \(worker nodes\). There are two groups of workers in general in such factory——one handles chopping the materials \(map\); the other group, with one worker orders the materials \(ordering\), all the rest handles processing and packaging \(reduce\).

To guarantee a non-stop streamline, master records the status of each worker, if each one has work in hand at all \(idle, or in-progress\), and whether they have finished their jobs \(completed\). If a worker is out-of-place, the job will be quickly re-assigned to a new worker. If a partition of data always causes issues to the workers, the master will just take this part out and let the workers finish with the rest. Once the map group finish their work, the job will be then passed down to the second group, reduce. 

At the end of the reduce task, there usually will be a ‘straggler’ due to multiple reasons. To make the production line more efficient, MapReduce uses some redundant nodes when the job is close to an end, and calls a stop when it gets result from any of the redundant nodes. With backups, the speed is increased dramatically. 

Lastly, to keep the system dynamic, each worker nodes get more than one job, and the fast workers get more work. In the mapping process, because it takes more time to pass the materials/data down to the target worker through the pipes \(the bandwidths is very scarce\), it makes more sense to design MapReduce in a way so that the input is stored on the local disks, and master takes the location information of the input files into account in order to schedule the map task to the workers that either has a replica of that input file, or the worker who is closest to such worker. In the reduce process, the master partitions the entire task into almost even partitions using hashing tricks, so that each node gets \(total\_key % R\) jobs, with R as the total number of reduce worknodes. R is generally smaller than M \(the total map worknodes\), because conventionally the finished reduced task will be passed down to another set of map worknodes to work on the next job.   


## The Google File System

Google File System \(GFS\) is a large distributed system which makes use of inexpensive commodity hardware to achieve a distributed filing system, which is the foundation to most of the services provided by google \(search, google drive, gmail, etc.\). HDFS is also built referring to the structure and the idea of GFS. 

GFS separates the whole system into 3 roles: client, master, and chunk server. Client is the interface for GFS to have access, and there in theory is only one master which coordinates the filing logistics, serving as the brain of the system. Chunk servers handle the real work of filing, and the size of it determines how large the GFS is. 

GFS partitions the document it saves into multiple chunks, and normally of multiple 64 MB size, with distinct indexes. Metadata contains the file information and index, while file content stores the actual contents in the file. For reliability purpose, GFS would make replica of each chunk to other chunk servers. As Client accesses GFS, it first accesses the master node, and retrieve the information of the chunk servers. This way, there is no data passing between the client and the master. 

The new entries are not changing the original columns, but are appended as a new column. The workloads supports large streaming reads and small random reads, and it doesn’t make much sense for GFS to deal with small data. Also, it has latency problems because GFS sets a higher volume of workload as priority.   


## Apache Spark: A Unified Engine for Big Data Processing

Most big data applications need to combine different processing types: streaming and loading, SQL queries, iterative machine learning algorithms, and finally graph and interpretations. Each process adds complexity to the procedure and when combining all steps together, it is considered inefficient as compared to the more unified engine, Apache Spark.

While MapReduce uses HDFS, Spark uses “Resilient Distributed Datasets” \(RDD\). Because RDD is augmented by a feature of data sharing, it is not necessary to write the data to storage and then pass it to another engine like the old way, which makes Spark very fast. Moreover, because of such feature, Spark uses in-memory calculations, allowing many applications to run on the same simultaneously and making it a more efficient way to combine processing tasks. When map, filter or groupby functions are applied to the data, the RDD are generated. Spark only executes after all of the transformations are identified and when it sees the most efficient plan for execution. 

Hadoop is aided by HDFS’s data replication and the checkpoint from the master node to achieve fault tolerance, but Spark uses “lineage”, so when there is lost partitions in the process and when Spark notices, it would rerun the operations of transformations on the original HDFS. In the big data sense, writing data over the network is much slower than writing it to RAM. 

Spark supports many libraries, including Spark SQL, for database query \(and tabular database is built with such engine\); MLlib, for machine learning models, GraphX, for graph computation interface.  
****

## YARN

In the old map reduce, because job tracker and task tracker takes up most of the work \(keep track of thousands of task ****trackers, hundreds of jobs, tens and thousands of map and reduce tasks\), it makes the system vulnerable. In other words, if the master node is out of order, the whole system would not work. The MRv2 system, or YARN, makes this part also distributed through introducing Resource Manager, Application Master and NodeManager combined way to increase its scalability. It provides HDFS an operating system. 

ResourceManager distributes and start the ApplicationMaster, and monitors ApplicationMaster, receives the submitted tasks from JobSubmitter, and appoints a container as application master based on information from NodeManager. Node manager maintains the container, and keep the heartbeat tracking with Resource Manager. Application Master handles all the work within a job cycle. 

Another upgrade because of YARN is the work nodes now can run any type of job, not just map or reduce. As long as the ApplicationMaster is valid, it can also run Giraph, Storm, Spark or MPI, etc.   
****

## Perspectives on the CAP Theorem

The CAP Theorem was introduced as a trade-off between consistency, availability, and partition tolerance. In a network subject to communication failures, it is impossible for any web service to implement an atomic read/write shared memory that guarantees a response to every request.

Consistency means each server gets back the right response to each request. For trivial services that requires only one machine, there is no coordination between different servers, and therefore it doesn’t fall within the CAP Theorem scope. For simple services, the operations are atomic, and the complicated services are the ones that cannot be specified by sequential specifications. A stands for Availability, which means each request eventually receive a response, but a late response is sometimes the same as no response. P means Partition-tolerance.

To overcome this, there are two approaches: sacrifice availability or consistency. For instance, for Amazon, before customers make purchase, they care more about availability; after the purchase, they care more about consistency; For Google, it is always important to guarantee availability \(correct operation, regardless of the network behavior\). This way, in practice, a large partition-prone network can be segmented into several subsystems, and each of the subsystem can choose a different trade-off.   
****

## Column-Stores vs. Row-Stores

Column-stores are more I/O efficient for read-only queries since they only have to read from disk \(or from memory\) those attributes accessed by a query. This is not done through simply vertically partitioning the schema or by indexing every column so that the columns are accessed independently. There is in fact something fundamental about the design of column-store systems that makes them better suited to data-warehousing workloads.  

There are several ways to implement a column-database design in a commercial row-oriented Database Management System. These include a fully vertically partitioned design, an ‘index only’ design, and a materialized view design. The first way is done through adding an integer indicating the position of each column to every table, the downside of this approach is it takes much of space. The second approach adds an additional index to every column of every table. The third is through creating an optimal set of materialized views for every query.

Compression algorithms perform better on data with low information entropy, that said, if the data of the same type are stored together. Compressing data also makes it more efficient for I/O. Moreover, compression allows the repeated values to be handled in one step, resulting in the ability of a query executor to perform the same operation on multiple column values at once, further reducing CPU costs.

Due to high tuple reconstruction costs, using a row-store system to implement a C-store is slow, although late materialization and compression can ameliorate this phenomenon. It is therefore a long way to go to fully address this issue.  


