# Setting up a Multi Node Hadoop Cluster

## Prerequisites

- Linux
- Java 8
- Hadoop

10.152.85.216

## Check ip address

Hadoop configuration files are in the folder etc/hadoop of hadoop

- Move to that folder

```bash
cd etc/hadoop
```

- List the files in that folder


```bash
ls
```

Among the listed files we will edit the 5 following: ``core-site.xml``, ``hdfs-site.xml``, ``mapred-site.xml``, ``yarn-site.xml``, ``hadoop-env.sh``



### Edit ``core-site.xml``


```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>
</configuration>
```

```core-site.xml``` informs Hadoop where NameNode runs in the cluster. It contains configuration settings of Hadoop core such as I/O settings that are common to HDFS & MapReduce.

### Edit ``hdfs-site..xml``

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.permission</name>
<value>false</value>
</property>
</configuration>
```

```hdfs-site.xml``` contains configuration settings of HDFS daemons (i.e. NameNode, DataNode, Secondary NameNode). It also includes the replication factor and block size of HDFS.

### Edit ``mapred-site..xml``

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
</configuration>
```

```mapred-site.xml``` contains configuration settings of MapReduce application like number of JVM that can run in parallel, the size of the mapper and the reducer process,  CPU cores available for a process, etc.

In some cases, mapred-site.xml file is not available. In that case, we have to create the mapred-site.xml file using mapred-site.xml template.

### Edit ``yarn-site..xml``

```xml
<?xml version="1.0">
<configuration>
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
</configuration>
```

```yarn-site.xml``` contains configuration settings of ResourceManager and NodeManager like application memory management size, the operation needed on program & algorithm, etc.

### Edit ``hadoop-env..sh``

```bash
# Set Java path
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
```

```hadoop-env.sh``` contains the environment variables that are used in the script to run Hadoop like Java home path, etc.


## Start up

### Move to the ```hadoop``` directory


### Format the name node

```bash
bin/hadoop namenode -format
```

This formats the HDFS via NameNode. This command is only executed for the first time. Formatting the file system means initializing the directory specified by the dfs.name.dir variable.

Never format, up and running Hadoop filesystem. You will lose all your data stored in the HDFS.  


### Move to the the sbin folder

```bash
cd sbin
```
### Start hadoop daemons

- Start NameNode

```bash
./hadoop-daemon.sh start namenode
```

The NameNode is the centerpiece of an HDFS file system. It keeps the directory tree of all files stored in the HDFS and tracks all the file stored across the cluster.

- Start DataNode

```bash
./hadoop-daemon.sh start datanode
```

On startup, a DataNode connects to the Namenode and it responds to the requests from the Namenode for different operations.

- Start ResourceManager

```bash
./yarn.sh start resourcemanager
```

ResourceManager is the master that arbitrates all the available cluster resources and thus helps in managing the distributed applications running on the YARN system. Its work is to manage each NodeManagers and the each application's ApplicationMaster.

- Start NodeManager

```bash
./yarn.sh start nodemanager
```

The NodeManager in each machine framework is the agent which is responsible for managing containers, monitoring their resource usage and reporting the same to the ResourceManager.

- Start JobHistoryServer

```bash
./mr-jobhistory-daemon.sh start historyserver
```

JobHistoryServer is responsible for servicing all job history related requests from client.

- Note that we may also have used the following command to do all in one and have all the hadoop daemons started in one time.

```bash
./start-all.sh 
```

- That command starts the following ressurces:
  
  - NameNode
  - SecondaryNameNode
  - DataNode
  - ResourceManager
  - NodeManager
  - JobHistoryServer


- Use the ``jps`` command to check the status of all the hadoop services, , specifically NameNode, DataNode, ResourceManager, NodeManager and JobHistoryServer.

```bash
jps
```

JPS stands for Java Virtual Machine(JVM) Process Status




## Acss the NameNode interface

Acess the name node interface via the URL [localhost:9870/dfshealth.html](localhost:9870/dfshealth.html)

- Note that some old versions of hadoop use the port ``50070`` instead of ``9870``


## Stop all hadoop daemons

- The easiest way to stop all hadoop daemons is the following

```bash
./stop-all.sh 
```

- Use the ``jps`` command to check the status of all the hadoop services
```bash
jps
```

[Source: edureka](https://www.edureka.co/blog/install-hadoop-single-node-hadoop-cluster)

[Solve issue with ssh](https://stackoverflow.com/questions/17335728/connect-to-host-localhost-port-22-connection-refused)





