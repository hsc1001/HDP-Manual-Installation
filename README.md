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

