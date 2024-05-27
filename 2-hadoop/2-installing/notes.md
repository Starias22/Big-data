# Hadoop installing

## Requirements

### Linux
### Java 8

Assure you have Java 8 installed

```bash
java -version
```

- If Java 8 is displayed as installed, you can move to the next section

- Else if any version of Java is installed  run the following command to install the version 8

    ```bash
    sudo apt-get install openjdk-8-jdk
    ```

- Else (in case the version displayed is not 8), check if there are other version(s) of Java installed

        ```bash
        sudo update-java-alternatives --list
        ```
    In that case:

    - If there is no Java 8 among these versions,  install it

    - Else if there is Java 8 among them

        - You must set it as default using the command

            ```bash
            sudo update-java-alternatives --set install openjdk-8-jdk
            ```

        - Now check once again the version of Java
            ```bash
            java -version
            ```

## Download

Run the following command to download the version 3.4.0 of hadoop

```bash
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz
```

## Installing

Run the following command to extract it

```bash
tar -xvf  hadoop-3.4.0.tar.gz
```

For simplicity, rename the folder from ``hadoop-3.4.0`` to ``hadoop-3.4.0``

```bash
mv  hadoop-3.4.0 hadoop
```


## Add the Hadoop and Java paths in the bash file

- Open the bashrc file

```bash
sudo nano .bashrc
```

- Check your ...

- Put the following contennt at the end of the file, according to your java path

```bash
# Set hadoop path
export HADOOP_HOME=$HOME/hadoop
export HADOOP_CONF_DIR=$HOME/hadoop/etc/hadoop
export HADOOP_MAPRED_HOME=$HOME/hadoop
export HADOOP_COMMON_HOME=$HOME/hadoop
export HADOOP_HDFS_HOME=$HOME/hadoop
export HADOOP_YARN_HOME=$HOME/hadoop
export PATH=$PATH:$HOME/hadoop/bin

# Set Java path
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-amd64/bin:$PATH
```

- Apply the changes made to the bashrc file

```bash
source .bashrc
```

- Now check hadoop version

```bash
hadoop version
```

