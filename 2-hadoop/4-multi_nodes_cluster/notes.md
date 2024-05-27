# Setting up a Single Node Hadoop Cluster

We will use the Elastic Compute Cloud (EC2) service of Amazone Web Services (AWS) to set up a multi node cloud.

Amazon EC2 allows you to create virtual machines, or instances, that run on the AWS Cloud.

Our hadoop cluster will be made up of four (4) nodes with:
* One (1) name node called **Master**
* One (1) secondary name node called **SecondaryMaster**
* Two (2) data nodes named **Slave1** and **Slave2**

## Prerequisites

- **A linux shell**
It will be used to execute
 If you don't have Linux you can use the CloudShell, which is a shell  proposed by AWS
- **An AWS account**

## Create the servers on AWS

The first step of the lab consists to create three servers/machines in AWS using EC2 service.

- Acess AWS EC2 service via  [https://eu-north-1.console.aws.amazon.com/ec2/](https://eu-north-1.console.aws.amazon.com/ec2/)

- Clik on the **Launch instance** button

On the **Launch instance**  page:

- Fill the **Name** field. 

  This is the name of your server or virtual machine.

  Name it **HadoopServer** for now.

- Move to the **Application and OS Images (Amazon Machine Image)** option 
  - Choose**Ubuntu**as operating system. 

  - Let the other options as default.

- Scroll down to the **Instance type** option 
  There is a dropdown arrow wich contains a list of predefined virtual machines with some characteristics such as the number of CPUs and the RAM size.

  You can scrolll down the dropdown list and choose c5.xlarge. It has 4 CPUs and 8 GB of RAM.

-  Move to the **Key pair (login)** option 
   This allow you to generate a key to login securely to your virtual machines. You can also select an already created key.

    - Create a new key name **hadoop_key** by letting the default option for the type of key (RSA) and the format of the file (.pem)
    - Click on the **Create key pai** button
    - Then, the **hadoop_key.perm**, containing your key pair will be downloaded to your local machine
- The **Network settings** allows you to define who can acess your virtual machines.

  Just check all the three boxes and let the Anywhere option of the dropdown list selected.
  
   
- There is the **Summary** option at the right

  This summurizes the characteristics of our virtual machine and also allows us to set the number of instances of virtual machines with the properties defined above we want to create. 

  As in our case we want to create 4 virtual machines, enter 4 as **Number of instances**
- Finally clik of the Lunch instance button to get the 4 instances of servers created


  
  

## Instances exploring

In this section we will explore our 4 instances.

- Clik on instances to acess your instances : You will see the list of the instances created with their characteristics among wich we have:
    
  - Name: which is the name of the instance, the one we edited during the creation
  - The Instance Id which is an unique identifier generated for the virtual machine. 
  - Instance state which represents the current state of the virtual machine. Now it is Running for all our 4 machines.
  - Instance type which is the type of machine
  - Status Check which may me Initializing or 2/2 checks passed	
  - Plublic IP4 DNS which represents the ***hostname*** of the machine.
   It is also a link that allows to expand the properties of the virtual machine.

-  Rename the machines

  Rename the machines: **Master**, **SecondaryMaster**, **Slave1**, **Slave2**

- Click on one of the Plublic IP4 DNS link: You will see several properties of the virtual machine among which two very important properties such as **Private IPv4 addresses** and  **Public IPv4 DNS**
  - **Private IPv4 addresses**  represents the IP address of the machine
  - **Public IPv4 DNS** represents the ***hostname*** of the machine

We will use them all the time.

We copy them to our cipboard just by clicking the copy icon.

## Get the **Private IPv4 addresse**  and **Public IPv4 DNS** of each virtual machine

Create a text file on your local machine to store the private IP address and the public IP DNS of each machine inside. You can name it **network_info.txt**

To get them, go the AWS EC2 and access the instances.

For each machine:

- Clik on the the Plublic IP4 DNS link to access it's properties in details
- Copy the private IP address to the clipboard and paste it into the file **network_info.txt**
- Copy the private IP address to the clipboard and paste it into the file **network_info.txt**

You can insert comments to make reading easier.

Your file content will look like:

```sh
# Master
172.31.42.110 
ec2-51-20-54-115.eu-north-1.compute.amazonaws.com


# SecondaryMaster
172.31.41.129
ec2-13-60-98-87.eu-north-1.compute.amazonaws.com


# Slave 1
172.31.41.228
ec2-13-60-83-86.eu-north-1.compute.amazonaws.com


# Slave2
172.31.39.207
ec2-13-49-66-73.eu-north-1.compute.amazonaws.com
```

Open a Linux terminal on your local machine


Move  the key file, haddop_key previously downloaded from the Download directory to your current directory

```sh
mv Downloads/hadoop_key.pem .
```

Copy the key file from your local machine to the master server using scp: Accept the question asked by pressing the enter key

```sh
scp -i hadoop_key.pem hadoop_key.pem ubuntu@ec2-51-20-54-115.eu-north-1.compute.amazonaws.com:
```

You will get a security warning. 

Before explaining that warning let us break down that command.

- scp means Secure CoPy

The scp command is used to copy a file from a machine to another

- The option i means identity 

When copying a file to a server, we should have a such access and we may then need to identify ourself.

The i option is ued to specify a identity file or a private key for identification

- The first hadoop_key represents the identity, which is in our case a file, containing a key

- The second hadoop_key represents the file to copy to the master server

- ubuntu@ec2-51-20-54-115.eu-north-1.compute.amazonaws.com is the server we are copying the file to, the master server:
  ubuntu represents the user name of the machine
  - As the OS we used to create our virtual machines is Ubuntu, all of them have ubuntu as (default) user
  - ec2-51-20-54-115.eu-north-1.compute.amazonaws.com is the master
  - Note that there is : at the end

To summarize, that command copy the file hadoop_key from our local machine to the root dicectory of the default user, named ubuntu, of our master server by using the same file, hadoop_key for authentification.


Now let come back to the security warning

***Permissions 0664 for 'hadoop_key.pem' are too open.
It is required that your private key files are NOT accessible by others.***

This means that the file we want to copy ie hadoop_key.pem is too open ie is not protected but must be not accessible by other users.

In 0664
 

4=write
6=4+2=read and write


- The first digit, 0 , represents the type of file, which is regular
- The second digit  represents the acess rule for the user which, is 6=4+2 ie read and write
- The third digit  represents the acess rule for the group, which is 6=4+2 ie read and write
- The fourth digit which is the last one,  represents the acess rule for theother  user which is 4 ie read

So  0664 means that our the user and it's group have read and write access on the file and that all other users have only read access to the file.

You can even confirm that by running the ls command with the option -l

```sh
ls -hadoop_key.pem 
```

That is why we see the warning

The output also obliges us to make the file not accessible by other users.

So we need to limt persmissions for the file:
Read permission is enough for the user
And no permission is needed for other user

That is done by using chmod

```bash
chmod 400 chmod 400 hadoop_key.pem 
```

In 400:

- The first digit  represents the acess rule for the user which, is 4 ie read and write
- The second  digit  represents the acess rule for the group, which is0 ie no acess
- The third digit which is the last one,  represents the acess rule for other users which is 0 ie no access.

Run the chmod command 

```sh
ls -hadoop_key.pem 
```

You will remark that the permissions have changed effectively

Run the scp command again.

```sh
scp -i hadoop_key.pem hadoop_key.pem ubuntu@ec2-51-20-54-115.eu-north-1.compute.amazonaws.com:
```

The file should have been copied now.

Open 4 terminals for access to our virtual machines via our local machine
- The first terminal will be used to acess the Master 
- The second terminal will be used to acess the SecondaryMaster
- The fthird terminal will be used to acess the Slave1
- The fourth terminal will be used to acess the Slave2

In the first terminal connect to the master server using ssh command

```sh
ssh -i hadoop_key.pem  ubuntu@ec2-51-20-54-115.eu-north-1.compute.amazonaws.com
```

That commands is simmilar to the scp one

- ssh means secure shell and is used to get connected to a remote server
- The i option is used to specify the identity, which is hadoop_key.pem
- The ubuntu@ec2-51-20-54-115.eu-north-1.compute.amazonaws.com represents the remote server we are getting connected to, that is the master server.

You should be connected.
ubuntu@ip-172-31-42-110:

In the same terminal, run the ls  command: You must see the hadoop_key.pem you have copied from your local machine

```bash
ls
```

In the same way, get connected to the SecondaryMaster, Slave1 and Slave2

- SecondaryMaster

```sh
ssh -i hadoop_key.pem  ubuntu@ec2-13-60-98-87.eu-north-1.compute.amazonaws.com
```

-Slave1  

```sh
ssh -i hadoop_key.pem  ubuntu@ec2-13-60-83-86.eu-north-1.compute.amazonaws.com
```

- Slave2 

```sh
ssh -i hadoop_key.pem  ubuntu@ec2-13-49-66-73.eu-north-1.compute.amazonaws.com
```

Now we need to edit the hosts file for all the 4 virtual machines.
The hosts file is located at /etc/hosts and contains Ip address with host name.

- For the master, run nano or vim and replace the localhost line by the following content

  - Run vim or nano

  ```sh
  sudo nano /etc/hosts
  ```
  - Replace the localhost line 

  ```sh
  172.31.42.110 ec2-51-20-54-115.eu-north-1.compute.amazonaws.com
  ```
  - Save the file and exit

- Do the same with the secondary master, and the two slaves.
  

Now for every virtual machine, update and upgrade all the available packages to their latest versions by running the command

 ```sh
 sudo apt update && sudo apt -y dist-upgrade
  ```

The -y option allows to answer yes to all the prompts that will be asking during the update and upgrade process

Assure that the processes complete for all the 4 machines.

Now we need to restart all our 4 virtual machines

- Go to AWS EC2 and access instances
- Check all the servers
- Select the Reboot instance option in the Instance state dropdown list
- Refresh instances by cliking on the Refresh instances icon

Now you will remark that all your shells are disconnnected from the virtual machines

Then can connected once again to them, by remembering for each of them the ssh command.

Get the version 8 of Java installed on every virtual machine, by running in every terminal the following command


 ```sh
sudo apt install -y  openjdk-8-jdk-headless
  ```

For every virtual machine, you can run java -version to check the installation

 ```sh
 java -version
  ```

On each virtual machine, download and extract hadoop gz package, and rename it to hadoop

- Dowload

```bash
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz 
```
- Extraction

```bash
tar -xvf  hadoop-3.4.0.tar.gz
```

- Renaming

```bash
mv  hadoop-3.4.0 hadoop
```

For all virtual machines, define the hadoop and Java home path insside the bashrc file using vim or nano

- Open the file with vim or nano 

```bash
sudo nano .bashrc 
```

- Put the following contennt at the end of the file

```bash
# Set hadoop path
export HADOOP_HOME=$HOME/hadoop
export HADOOP_CONF=$HOME/hadoop/conf
export PATH=$PATH:$HADOOP_HOME/bin

# Set Java path
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
```

- Save the file and exit the editor

- Apply the changes made to the bashrc file

```sh
source .bashrc
```

- Now check hadoop version

```bash
hadoop version
```

Now only on the master server, we will generate a key of type RSA using the ssh-keygen command with the t option

```bash
ssh-keygen -t rsa
```

Now copy the path where the key has been saved  /home/ubuntu/.ssh/id_rsa.pub and use scp command to share the rda key file with all the 3 other virtual machines, on the same file path

- SecondaryMaster

```sh
scp -i hadoop_key.pem /home/ubuntu/.ssh/id_rsa.pub ubuntu@ec2-13-60-98-87.eu-north-1.compute.amazonaws.com:/home/ubuntu/.ssh/id_rsa.pub 
```

- Slave1

```sh
scp -i hadoop_key.pem /home/ubuntu/.ssh/id_rsa.pub ubuntu@ec2-13-60-83-86.eu-north-1.compute.amazonaws.com:/home/ubuntu/.ssh/id_rsa.pub 
```

- Slave2

```sh
scp -i hadoop_key.pem /home/ubuntu/.ssh/id_rsa.pub ubuntu@ec2-13-49-66-73.eu-north-1.compute.amazonaws.com:/home/ubuntu/.ssh/id_rsa.pub 
```

Now afor each server, add the generated key to the list of authorized keys



```bash
cat .ssh/authorized_keys 
```

By doing that, the generated key can be used for authentication on these servers.


Now let us edit ssh configuration file for our name node. 

This will facilitate connections to our servers. By doing that we will be able to acess the servers  without using any password or ssh key.

Open the ssh configuration file using vim or nano

```bash
sudo nano .ssh/config
```


```bash
Host Master
    HostName ec2-51-20-54-115.eu-north-1.compute.amazonaws.com
    User ubuntu
    IdentityFile ~/.ssh/id_rsa

Host SecondaryMaster
    HostName ec2-13-60-98-87.eu-north-1.compute.amazonaws.com
    User ubuntu
    IdentityFile ~/.ssh/id_rsa

Host Slave1
    HostName ec2-13-60-83-86.eu-north-1.compute.amazonaws.com
    User ubuntu
    IdentityFile ~/.ssh/id_rsa

Host Slave2
    HostName ec2-13-49-66-73.eu-north-1.compute.amazonaws.com
    User ubuntu
    IdentityFile ~/.ssh/id_rsa
```

Now for each machine,  create a parent directory /usr/local/hadoop/hdfs/data and set it's user and group owner to ubuntu.

```bash
sudo mkdir -p /usr/local/hadoop/hdfs/data && sudo chown ubuntu:ubuntu /usr/local/hadoop/hdfs/data/
```








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


### Edit ``hadoop-env..sh``

```bash
# Set Java path
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
```

```hadoop-env.sh``` contains the environment variables that are used in the script to run Hadoop like Java home path, etc.



### Edit ``core-site.xml``


``xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
<name>fs.default.name</name>
<value>hdfs://ec2-51-20-54-115.eu-north-1.compute.amazonaws.com:9000</value>
</property>
</configuration>
``

```core-site.xml``` informs Hadoop where NameNode runs in the cluster. It contains configuration settings of Hadoop core such as I/O settings that are common to HDFS & MapReduce.

### Edit ``hdfs-site..xml``

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
<name>dfs.replication</name>
<value>2</value>
</property>
<property>
<name>dfs.permission</name>
<value>false</value>
</property>
<property>
<name>dfs.namenode.name.dir</name>
<value>file:///usr/local/hadoop/hdfs/data</value>
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
<value>mapred_shuffle</value>
</property>
<property>
<name>yarn.resourcemanager.hostname</name>
<value>ec2-51-20-54-115.eu-north-1.compute.amazonaws.com</value>
</property>
</configuration>
```

```yarn-site.xml``` contains configuration settings of ResourceManager and NodeManager like application memory management size, the operation needed on program & algorithm, etc.

Copy all the configuration files edited above to the other virtual machines

- Copy to the secondary master

```bash
scp hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml yarn-site.xml SecondaryMaster:/home/ubuntu/hadoop
```

- Copy to the first slave

```bash
scp hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml yarn-site.xml Slave1:/home/ubuntu/hadoop/etc/hadoop
```

- Copy to the second slave

```bash
scp hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml yarn-site.xml Slave2:/home/ubuntu/hadoop/etc/hadoop
```

On the master, create in the hadoop configuration folder two files named masters and slaves. They will contain the hostname/DNS of all of our virtual machhnes

- Move to hadoop configuration folder

```bash
 cd hadoop/etc/hadoop/
```

- Create and open a file named masters using vim or nano
```bash
nano masters
```

- Insert inside that file the DNS/hostname of the master and the secondary master.

```bash
ec2-51-20-54-115.eu-north-1.compute.amazonaws.com
ec2-13-60-98-87.eu-north-1.compute.amazonaws.com
```
- Save the file and exit

- Create and open a file named slaves using vim or nano

```bash
nano masters
```

- Insert inside that file the DNS/hostname of the two slaves.

```bash
ec2-13-60-83-86.eu-north-1.compute.amazonaws.com
ec2-13-49-66-73.eu-north-1.compute.amazonaws.com
```
- Save the file and exit

- Copy the masters and slaves files from the master server's configuration folder to the secondary master server's configuration folder.

```bash
scp masters slaves SecondaryMaster:/home/ubuntu/hadoop/etc/hadoop/
```
- Copy the  slaves files from the master server's configuration folder to the first and second slave configuration folder.

```bash
scp slaves Slave1:/home/ubuntu/hadoop//etc/hadoop/
```

```bash
scp slaves Slave2:/home/ubuntu/hadoop/etc/hadoop/
```

Go to the first slave's terminal and move to it's hadoop configuration folder

User vim or nano to modify the slaves file such as to maintain only it's hastname inside

Do the  same with the second slave




## Start up

### Move to the ```hadoop``` directory


### Format the name node

```bash
hdfs namenode -format
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
./yarn-daemon.sh start resourcemanager
```

ResourceManager is the master that arbitrates all the available cluster resources and thus helps in managing the distributed applications running on the YARN system. Its work is to manage each NodeManagers and the each application's ApplicationMaster.

- Start NodeManager

```bash
./yarn-daemon.sh start nodemanager
```

The NodeManager in each machine framework is the agent which is responsible for managing containers, monitoring their resource usage and reporting the same to the ResourceManager.

- Start JobHistoryServer

```bash
./mr-jobhistory-daemon.sh start historyserver
```

JobHistoryServer is responsible for servicing all job history related requests from client.




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

[Youtube video](https://youtu.be/FFK8HvgMZec)





