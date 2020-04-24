# HDP-Manual-Installation-
Guide for Manual Installation for Learners 
Setup a 4 cluster in OpenStack https://172.26.132.10/project/instances/ 
Cluster Architecture and Host wise components :- 

In this Lab exercise, we are going to install HDP services manually and test them all. 

I have 4 nodes from the support Lab i.e. 

    hchauhan-1.openstack.local
    hchauhan-2.openstack.local
    hchauhan-3.openstack.local
    hchauhan-4.openstack.local
    
    
## Prerequisite on the nodes. 
  ### Install and configure JAVA on all the nodes. 
      copy the JAVA files from the squadron cluster, You could use the same JAVA path.
      # mkdir /usr/jdk64
      # ls -ltr /usr/jdk64/jdk1.8.0_112/

### Download the HDP repository
    # wget -nv http://public-repo-1.hortonworks.com/HDP/centos7/2.x/updates/2.6.3.0/hdp.repo

### Creating System Users and Groups
    #groupadd hadoop
    #useradd -G hadoop hdfs
    #useradd -G hadoop yarn
    #useradd -G hadoop mapred
    #useradd -G hadoop hive
    #useradd -G hadoop hcat
    #useradd -G hadoop knox
    #useradd -G hadoop oozie
    #useradd -G hadoop zookeeper
    #useradd -G hadoop sqoop
    #useradd -G hadoop falcon
    #useradd -G hadoop hbase


Now we are going to start service wise to install packages and create the directories and will test each service. 

## Zookeeper Service

We are going to have the zookeeper services on 3 nodes of the cluster

    hchauhan-1.openstack.local
    hchauhan-2.openstack.local
    hchauhan-3.openstack.local
 
### Zookeeper Directories 
 
    ZooKeeper stores data - /hadoop/zookeeper/data
    Zookeeper Conf Directory - /etc/hadoop/conf 
    ZooKeeper logs - /var/log/zookeeper
    PID Zookeeper Directory - /var/run/zookeeper
 
 #### Installing Apache ZooKeeper

   On all nodes of the cluster that you have identified as ZooKeeper servers, type: 

    For RHEL/CentOS/Oracle Linux
    yum install zookeeper-server

   Create the zookeeper Directories 
 
    mkdir -p /var/log/zookeeper;
    chown -R zookeeper:hadoop /var/log/zookeeper;
    chmod -R 755 /var/log/zookeeper;

    mkdir -p /var/run/zookeeper;
    chown -R zookeeper:hadoop /var/run/zookeeper;
    chmod -R 755 /var/run/zookeeper;

    mkdir -p /hadoop/zookeeper/data;
    chmod -R 755 /hadoop/zookeeper/data;
    chown -R zookeeper:hadoop /hadoop/zookeeper/data;


Initialize the ZooKeeper data directories with the 'myid' file. Create one file per ZooKeeper server, and put the number of that server in each file:

    vi /hadoop/zookeeper/data/myid
        In the myid file on the first server, enter the corresponding number: 1 
        In the myid file on the second server, enter the corresponding number: 2
        In the myid file on the third server, enter the corresponding number: 3 
    
Files under /etc/zookeeper/conf 

    [zookeeper@hchauhan-1 ~]$ cd /etc/zookeeper/conf/
    [zookeeper@hchauhan-1 conf]$ ls -ltr
    total 20
    -rw-r--r--. 1 root root  922 Oct 30  2017 zoo_sample.cfg
    -rw-r--r--. 1 root root 2180 Oct 30  2017 log4j.properties
    -rw-r--r--. 1 root root  535 Oct 30  2017 configuration.xsl
    drwxr-xr-x. 2 root root  141 Apr  8 10:47 backup
    -rw-r--r--. 1 root root 1326 Apr  8 10:53 zoo.cfg
    -rwxr-xr-x. 1 root root 1078 Apr  8 11:28 zookeeper-env.sh

Contents for the important files and Directories 

#### zoo.cfg
    maxClientCnxns=50
    # The number of milliseconds of each tick
    tickTime=2000
    # The number of ticks that the initial
    # synchronization phase can take
    initLimit=10
    # The number of ticks that can pass between
    # sending a request and getting an acknowledgement
    syncLimit=5
    # the directory where the snapshot is stored.
    dataDir=/hadoop/zookeeper/data
    # the port at which the clients will connect
    clientPort=2181
    server.1=hchauhan-1.openstack.local:2888:3888
    server.2=hchauhan-2.openstack.local:2888:3888
    server.3=hchauhan-3.openstack.local:2888:3888
    
#### zookeeper-env.sh
    export JAVA_HOME=/usr/jdk64/jdk1.8.0_112
    export ZOOKEEPER_HOME=/usr/hdp/current/zookeeper-server
    export ZOO_LOG_DIR=/var/log/zookeeper
    export SERVER_JVMFLAGS=-Xmx1024m
    export ZOOPIDFILE=/var/run/zookeeper/zookeeper_server.pid
    export JAVA=$JAVA_HOME/bin/java
    CLASSPATH=$CLASSPATH:$ZOOKEEPER_HOME/*
    
    
### Start the zookeeper services on hosts which are identified to run zookeeper 

    [root@hchauhan-3 yum.repos.d]# su - zookeeper
    [zookeeper@hchauhan-3 ~]$ export ZOOCFGDIR=/etc/zookeeper/conf
    [zookeeper@hchauhan-3 ~]$ export ZOOCFG=zoo.cfg
    [zookeeper@hchauhan-3 ~]$ source /etc/zookeeper/conf/zookeeper-env.sh;
    [zookeeper@hchauhan-3 ~]$ /usr/hdp/current/zookeeper-server/bin/zkServer.sh start
    ZooKeeper JMX enabled by default
    Using config: /etc/zookeeper/conf/zoo.cfg
    Starting zookeeper ... STARTED

Command to verify the zookeeper process on all nodes 

    # ps -ef | grep zookeeper
    
Check whether the logs are getting updates and run directory have the zookeeper pid. 

    cd /usr/hdp/current/zookeeper-client/bin/ 
    ./zkCli.sh
The above steps will start  the Zookeeper command line interface through which we can interact with Zookeeper. Read through the logs printed out to the console. Create the znode using the below command 
 
    create -e /zk101en “ZKEnTest”

Ensure that the znode is created successfully by listing it. Next, exit out of zkCli and log back in. Once logged in, try to list the newly created znode - /zk101en. If not then try to find out why?

## Installing HDFS, YARN, and MapReduce

Install the Hadoop Packages

Execute the following command on all cluster nodes.

    #yum install hadoop hadoop-hdfs hadoop-libhdfs hadoop-yarn hadoop-mapreduce hadoop-client openssl

### HDFS Service 

        NameNode -  hchauhan-1.openstack.local
                 
        Secondary NameNode - hchauhan-2.openstack.local
        
        Journal Node    - hchauhan-1.openstack.local
                        - hchauhan-2.openstack.local
                        - hchauhan-3.openstack.local
                        
        ZKFC            - hchauhan-1.openstack.local
                        - hchauhan-2.openstack.local
                        
        Datanode - hchauhan-1.openstack.local
                   hchauhan-2.openstack.local
                   hchauhan-3.openstack.local
                   hchauhan-4.openstack.local


Use the following instructions to create appropriate directories:
Create the NameNode Directories

On the node that hosts the NameNode service, execute the following commands:

    mkdir -p /hadoop/hdfs/namenode;
    chown -R hdfs:hadoop /hadoop/hdfs/namenode;
    chmod -R 755 /hadoop/hdfs/namenode;

Create the SecondaryNameNode Directories

    mkdir -p /hadoop/hdfs/secondarynamenode;
    chown -R hdfs:hadoop /hadoop/hdfs/secondarynamenode;
    chmod -R 755 /hadoop/hdfs/secondarynamenode;

Create DataNode directories

    mkdir -p /hadoop/hdfs/data;
    chown -R hdfs:hadoop /hadoop/hdfs/data;
    chmod -R 750 /hadoop/hdfs/data;

HDFS Log Directories 
At all nodes, execute the following commands:

    mkdir -p /var/log/hadoop/hdfs;
    chown -R hdfs:hadoop /var/log/hadoop/hdfs;
    chmod -R 755 /var/log/hadoop/hdfs;

 HDFS Process
At all nodes, execute the following commands:

    mkdir -p /var/run/hadoop/hdfs;
    chown -R hdfs:hadoop /var/run/hadoop/hdfs;
    chmod -R 755 /var/run/hadoop/hdfs;

Download the companion files from the below link which will be helpful in setting up the Hadoop configuration files. 

    cd /root 
    wget http://public-repo-1.hortonworks.com/HDP/tools/2.6.0.3/hdp_manual_install_rpm_helper_files-2.6.0.3.8.tar.gz
    tar zxvf hdp_manual_install_rpm_helper_files-2.6.0.3.8.tar.gz
    
    [root@hchauhan-4 hdp_manual_install_rpm_helper_files-2.6.0.3.8]# ls -ltr
        total 8
        drwxr-xr-x.  2 hdfs users  74 Apr  3  2017 scripts
        -rw-r--r--.  1 hdfs users 623 Apr  3  2017 readme.txt
        drwxr-xr-x. 12 hdfs users 148 Apr  3  2017 configuration_files
        -rw-r--r--.  1 hdfs users 154 Apr  3  2017 HDP-CHANGES.txt
        [root@hchauhan-4 hdp_manual_install_rpm_helper_files-2.6.0.3.8]# cd configuration_files/
        [root@hchauhan-4 configuration_files]# ls -ltr
        total 4
        drwxr-xr-x. 2 hdfs users   94 Apr  3  2017 zookeeper
        drwxr-xr-x. 2 hdfs users   52 Apr  3  2017 webhcat
        drwxr-xr-x. 2 hdfs users  138 Apr  3  2017 sqoop
        drwxr-xr-x. 2 hdfs users   70 Apr  3  2017 pig
        drwxr-xr-x. 2 hdfs users  150 Apr  3  2017 oozie
        drwxr-xr-x. 4 hdfs users   36 Apr  3  2017 nagios
        drwxr-xr-x. 2 hdfs users  127 Apr  3  2017 hive
        drwxr-xr-x. 2 hdfs users  253 Apr  3  2017 hbase
        drwxr-xr-x. 4 hdfs users   36 Apr  3  2017 ganglia
        drwxr-xr-x. 2 hdfs users 4096 Apr  3  2017 core_hadoop
        
        [root@hchauhan-4 configuration_files]# cd core_hadoop/
        [root@hchauhan-4 core_hadoop]# ls -ltr
        total 84
        -rw-r--r--. 1 hdfs users 4143 Apr  3  2017 yarn-site.xml
        -rw-r--r--. 1 hdfs users 4340 Apr  3  2017 yarn-env.sh
        -rw-r--r--. 1 hdfs users 4233 Apr  3  2017 mapred-site.xml
        -rw-r--r--. 1 hdfs users  279 Apr  3  2017 mapred-queue-acls.xml
        -rw-r--r--. 1 hdfs users 5734 Apr  3  2017 log4j.properties
        -rwxr-xr-x. 1 hdfs users 2527 Apr  3  2017 health_check
        -rw-r--r--. 1 hdfs users 4018 Apr  3  2017 hdfs-site.xml
        -rwxr-xr-x. 1 hdfs users 5007 Apr  3  2017 hadoop-policy.xml
        -rwxr-xr-x. 1 hdfs users 1421 Apr  3  2017 hadoop-metrics2.properties-GANGLIA
        -rwxr-xr-x. 1 hdfs users 1515 Apr  3  2017 hadoop-metrics2.properties
        -rw-r--r--. 1 hdfs users 5734 Apr  3  2017 hadoop-env.sh
        -rw-r--r--. 1 hdfs users 2339 Apr  3  2017 core-site.xml
        -rw-r--r--. 1 hdfs users  171 Apr  3  2017 container-executor.cfg
        -rw-r--r--. 1 hdfs users 1020 Apr  3  2017 commons-logging.properties
        -rw-r--r--. 1 hdfs users 1474 Apr  3  2017 capacity-scheduler.xml

In the temporary directory, locate the following files and modify the properties based on your environment.

Search for TODO in the files for the properties to replace. For further information, see "Define Environment Parameters" in this guide.

Edit core-site.xml and modify the following properties:

    <property>
         <name>fs.defaultFS</name>
         <value>hdfs://$namenode.full.hostname:8020</value>
         <description>Enter your NameNode hostname</description>
    </property>

    <property>
         <name>hdp.version</name>
         <value>${hdp.version}</value>
         <description>Replace with the actual HDP version</description>
    </property>

Edit hdfs-site.xml and modify the following properties:

    <property>
         <name>dfs.namenode.name.dir</name>
         <value>/grid/hadoop/hdfs/nn,/grid1/hadoop/hdfs/nn</value>
         <description>Comma-separated list of paths. Use the list of directories from $DFS_NAME_DIR. For example, /grid/hadoop/hdfs/nn,/grid1/hadoop/hdfs/nn.</description>
    </property>

    <property>
         <name>dfs.datanode.data.dir</name>
         <value>file:///grid/hadoop/hdfs/dn, file:///grid1/hadoop/hdfs/dn</value>
         <description>Comma-separated list of paths. Use the list of directories from $DFS_DATA_DIR. For example, file:///grid/hadoop/hdfs/dn, file:///grid1/ hadoop/hdfs/dn.</description>
    </property>

    <property>
         <name>dfs.namenode.http-address</name>
         <value>$namenode.full.hostname:50070</value>
         <description>Enter your NameNode hostname for http access.</description>
    </property>

    <property>
         <name>dfs.namenode.secondary.http-address</name>
         <value>$secondary.namenode.full.hostname:50090</value>
         <description>Enter your Secondary NameNode hostname.</description>
    </property>

    <property>
         <name>dfs.namenode.checkpoint.dir</name>
         <value>/grid/hadoop/hdfs/snn,/grid1/hadoop/hdfs/snn,/grid2/hadoop/hdfs/snn</value>
         <description>A comma-separated list of paths. Use the list of directories from $FS_CHECKPOINT_DIR. For example, /grid/hadoop/hdfs/snn,sbr/grid1/hadoop/hdfs/ snn,sbr/grid2/hadoop/hdfs/snn </description>
    </property>

    <property>
         <name>dfs.namenode.checkpoint.edits.dir</name>
         <value>/grid/hadoop/hdfs/snn,/grid1/hadoop/hdfs/snn,/grid2/hadoop/hdfs/snn</value>
         <description>A comma-separated list of paths. Use the list of directories from $FS_CHECKPOINT_DIR. For example, /grid/hadoop/hdfs/snn,sbr/grid1/hadoop/hdfs/ snn,sbr/grid2/hadoop/hdfs/snn </description>
    </property>

    <property>
         <name>dfs.namenode.rpc-address</name>
         <value>namenode_host_name:8020>
         <description>The RPC address that handles all clients requests.</description.>
    </property>

    <property>
         <name>dfs.namenode.https-address</name>
         <value>namenode_host_name:50470>
         <description>The namenode secure http server address and port.</description.>
    </property>

 Append the following to /etc/profile:

    touch $HADOOP_CONF_DIR/dfs.exclude
    JAVA_HOME=<java_home_path>
    export JAVA_HOME
    HADOOP_CONF_DIR=/etc/hadoop/conf/
    export HADOOP_CONF_DIR
    export PATH=$PATH:$JAVA_HOME:$HADOOP_CONF_DIR
    
 On all hosts in your cluster, create the Hadoop configuration directory:

    rm -rf $HADOOP_CONF_DIR
    mkdir -p $HADOOP_CONF_DIR
    where $HADOOP_CONF_DIR is the directory for storing the Hadoop configuration files. For example, /etc/hadoop/conf.

Copy all the configuration files from a temporary folder created above to $HADOOP_CONF_DIR.

Set the appropriate permissions:

    chown -R $HDFS_USER:$HADOOP_GROUP $HADOOP_CONF_DIR/../
    chmod -R 755 $HADOOP_CONF_DIR/../
    
### Start the HDFS services 
    
 Modify the JAVA_HOME value in the hadoop-env.sh file:

    export JAVA_HOME=/usr/jdk64/jdk1.8.0_112/

Execute the following commands on the NameNode host machine:

    su - $HDFS_USER
    /usr/hdp/current/hadoop-hdfs-namenode/../hadoop/bin/hdfs namenode -format
    /usr/hdp/current/hadoop-hdfs-namenode/../hadoop/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR start namenode

Execute the following commands on the SecondaryNameNode:

    su - $HDFS_USER
    /usr/hdp/current/hadoop-hdfs-secondarynamenode/../hadoop/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR start secondarynamenode

Execute the following commands on all DataNodes:

    su - $HDFS_USER
    /usr/hdp/current/hadoop-hdfs-datanode/../hadoop/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR start datanode
    Where $HADOOP_CONF_DIR is the directory for storing the Hadoop configuration files. For example, /etc/hadoop/conf.

Where $HDFS_USER is the HDFS user, for example, hdfs.   
 
#### Smoke Test HDFS
Determine if you can reach the NameNode server with your browser:

http://$namenode.full.hostname:50070

Create the hdfs user directory in HDFS:

    su - $HDFS_USER
    hdfs dfs -mkdir -p /user/hdfs
Try copying a file into HDFS and listing that file:

    su - $HDFS_USER
    hdfs dfs -copyFromLocal /etc/passwd passwd
    hdfs dfs -ls
Use the Namenode web UI and the Utilities menu to browse the file system.

Verify the Namenode Process, verify the logs for Datanodes, Namenode and secondary Namenode. 

## Yarn Service 



