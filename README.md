# HDP-Manual-Installation
Guide for Manual Installation for Learners 
Setup a 4 cluster in OpenStack 
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

## Yarn and MapReduce Service 

    Resource Manager - hchauhan-1.openstack.local
    NodeManager - hchauhan-2.openstack.local
                 hchauhan-3.openstack.local
                 hchauhan-4.openstack.local


#### Directories for RM and NM
YARN NodeManager Local directories :- At each ResourceManager and all DataNodes

    mkdir -p /hadoop/yarn/local;
    chown -R yarn:hadoop /hadoop/yarn/local;
    chmod -R 755 /hadoop/yarn/local;

At each ResourceManager and all DataNodes, execute the following commands:

    mkdir -p /hadoop/yarn/logs;
    chown -R yarn:hadoop /hadoop/yarn/logs;
    chmod -R 755 /hadoop/yarn/logs;



At all nodes, execute the following commands:

    mkdir -p /var/log/hadoop/yarn;
    chown -R yarn:hadoop /var/log/hadoop/yarn;
    chmod -R 755 /var/log/hadoop/yarn;

At all nodes, execute the following commands:

    mkdir -p /var/run/hadoop/yarn;
    chown -R yarn:hadoop /var/run/hadoop/yarn;
    chmod -R 755 /var/run/hadoop/yarn;

JobHistory Server Logs

    At all nodes, execute the following commands:
    mkdir -p /var/log/hadoop/mapred;
    chown -R mapred:hadoop /var/log/hadoop/mapred;
    chmod -R 755 /var/log/hadoop/mapred;


JobHistory Server Process ID

    At all nodes, execute the following commands:
    mkdir -p /var/run/hadoop/mapred;
    chown -R mapred:hadoop /var/run/hadoop/mapred;
    chmod -R 755 /var/run/hadoop/mapred;

#### Configure YARN and MapReduce
After you install Hadoop, modify your configs.
As the HDFS user, for example 'hdfs', upload the MapReduce tarball to HDFS.

    su - $HDFS_USER
    hdfs dfs -mkdir -p /hdp/apps/<hdp_version>/mapreduce/
    hdfs dfs -put /usr/hdp/current/hadoop-client/mapreduce.tar.gz /hdp/apps/<hdp_version>/mapreduce/
    hdfs dfs -chown -R hdfs:hadoop /hdp
    hdfs dfs -chmod -R 555 /hdp/apps/<hdp_version>/mapreduce
    hdfs dfs -chmod 444 /hdp/apps/<hdp_version>/mapreduce/mapreduce.tar.gz Where $HDFS_USER is the HDFS user, for example hdfs, and <hdp_version> is the current HDP version, for example 2.6.0.0. 


Copy mapred-site.xml from the companion files and make the following changes to mapred-site.xml:

    Add: <property>
         <name>mapreduce.admin.map.child.java.opts</name>
         <value>-server -Djava.net.preferIPv4Stack=true -Dhdp.version=${hdp.version}</value>
         <final>true</final>
    </property>

    Modify the following existing properties to include ${hdp.version}:  <property>
         <name>mapreduce.admin.user.env</name>
         <value>LD_LIBRARY_PATH=/usr/hdp/${hdp.version}/hadoop/lib/native:/usr/hdp/${hdp.version}/hadoop/lib/native/Linux-amd64-64</value>
    </property>

    <property>
         <name>mapreduce.application.framework.path</name>
         <value>/hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework</value>
    </property>

    <property>
    <name>mapreduce.application.classpath</name>
    <value>$PWD/mr-framework/hadoop/share/hadoop/mapreduce/*:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/lib/*:$PWD/mr-framework/hadoop/share/hadoop/common/*:$PWD/mr-framework/hadoop/share/hadoop/common/lib/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/lib/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/lib/*:/usr/hdp/${hdp.version}/hadoop/lib/hadoop-lzo-0.6.0.${hdp.version}.jar:/etc/hadoop/conf/secure</value>
    </property>


Copy yarn-site.xml from the companion files and modify:

    <property>
         <name>yarn.application.classpath</name>
         <value>$HADOOP_CONF_DIR,/usr/hdp/${hdp.version}/hadoop-client/*,
         /usr/hdp/${hdp.version}/hadoop-client/lib/*,
         /usr/hdp/${hdp.version}/hadoop-hdfs-client/*,
         /usr/hdp/${hdp.version}/hadoop-hdfs-client/lib/*,
         /usr/hdp/${hdp.version}/hadoop-yarn-client/*,
         /usr/hdp/${hdp.version}/hadoop-yarn-client/lib/*</value>
    </property>  

Start YARN

As $YARN_USER, run the following command from the ResourceManager server:

    su -l yarn -c "/usr/hdp/current/hadoop-yarn-resourcemanager/sbin/yarn-daemon.sh --config /etc/hadoop/conf start resourcemanager" 
As $YARN_User, run the following command from all NodeManager nodes:

    su -l yarn -c "/usr/hdp/current/hadoop-yarn-nodemanager/sbin/yarn-daemon.sh --config /etc/hadoop/conf start nodemanager"


 Start MapReduce JobHistory Server

    Change permissions on the container-executor file. 
    chown -R root:hadoop /usr/hdp/current/hadoop-yarn*/bin/container-executor
    chmod -R 6050 /usr/hdp/current/hadoop-yarn*/bin/container-executor

Execute these commands from the JobHistory server to set up directories on HDFS: su $HDFS_USER

    hdfs dfs -mkdir -p /mr-history/tmp
    hdfs dfs -mkdir -p /mr-history/done

    hdfs dfs -chmod 1777 /mr-history
    hdfs dfs -chmod 1777 /mr-history/tmp
    hdfs dfs -chmod 1770 /mr-history/done

    hdfs dfs -chown $MAPRED_USER:$MAPRED_USER_GROUP /mr-history
    hdfs dfs -chown $MAPRED_USER:$MAPRED_USER_GROUP /mr-history/tmp
    hdfs dfs -chown $MAPRED_USER:$MAPRED_USER_GROUP /mr-history/done

    Where
        $MAPRED_USER : mapred
        $MAPRED_USER_GROUP: mapred or hadoop

    hdfs dfs -mkdir -p /app-logs
    hdfs dfs -chmod 1777 /app-logs
    hdfs dfs -chown $YARN_USER:$HADOOP_GROUP /app-logs

    Where
    $YARN_USER : yarn
    $HADOOP_GROUP: hadoop 
Run the following command from the JobHistory server: 
    
    su -l $YARN_USER -c "/usr/hdp/current/hadoop-mapreduce-historyserver/sbin/mr-jobhistory-daemon.sh --config /etc/hadoop/conf start historyserver"


#### Smoke Test MapReduce

Browse to the ResourceManager: http://$resourcemanager.full.hostname:8088/ 

Create a $CLIENT_USER in all of the nodes and add it to the users group. 
  
    useradd client
    usermod -a -G users client 

As the HDFS user, create a /user/$CLIENT_USER.

    sudo su - $HDFS_USER
    hdfs dfs -mkdir /user/$CLIENT_USER
    hdfs dfs -chown $CLIENT_USER:$CLIENT_USER /user/$CLIENT_USER
    hdfs dfs -chmod -R 755 /user/$CLIENT_USER 
Run the smoke test as the $CLIENT_USER. Using Terasort, sort 10GB of data.

    su - $CLIENT_USER
    /usr/hdp/current/hadoop-client/bin/hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples-*.jar teragen 10000 tmp/teragenout
    /usr/hdp/current/hadoop-client/bin/hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples-*.jar terasort tmp/teragenout tmp/terasortout 

Test whether you are able to submit the job succesfully or not, Please troublesshoot the error which you are getting. 


## Install HBase 
    Hbase Master :          hchauhan-1.openstack.local
    Hbase RegionServer:     hchauhan-2.openstack.local
    
#### Install the packges on the nodes. 
    [root@hchauhan-1 ~]# yum install hbase
    [root@hchauhan-2 ~]# yum install hbase
    
#### Create the Directories for Logs and PID on Master and RegionServer 
    [root@hchauhan-1 ~]# mkdir -p /var/log/hbase
    [root@hchauhan-1 ~]# chown -R hbase:hadoop /var/log/hbase
    [root@hchauhan-1 ~]# chmod -R 755 /var/log/hbase
    [root@hchauhan-1 ~]# mkdir -p  /var/run/hbase
    [root@hchauhan-1 ~]# chown -R hbase:hadoop /var/run/hbase
    [root@hchauhan-1 ~]# chmod -R 755 /var/run/hbase
    
#### Set Up the Configuration Files
    Copy hbase-site.xml and other files from the companion files under /hdp_manual_install_rpm_helper_files-2.6.0.3.8/configuration_files/hbase and make the following changes to hbase-site.xml:
    
   Edit hbase-site.xml and modify the following properties:

    <property>
          <name>hbase.rootdir</name>
          <value>hdfs://$hbase.namenode.full.hostname:8020/apps/hbase/data</value>
          <description>Enter the HBase NameNode server hostname</description>
     </property>

    <property>
          <name>hbase.zookeeper.quorum</name>
          <value>$zk.server1.full.hostname,$zk.server2.full.hostname,$zk.server3.full.hostname</value>
          <description>Comma separated list of ZooKeeper servers (match to what is specified in zoo.cfg but without portnumbers)</description>
    </property>
    
 Move the files to the Regionserver and the Hbase Master node. 
 
#### Start the Hbase Master and RegionServer and Test.


Execute the following command from the HBase Master node:

    su -l hbase -c "/usr/hdp/current/hbase-master/bin/hbase-daemon.sh start master; sleep 25"

Execute the following command from each HBase Region Server node:

    su -l hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"

#### Smoke Test HBase.

From a terminal, enter:

    su - hbase
    hbase shell
    In the HBase shell, enter the following command:

    status 'detailed'
    
## Install Knox 
 
     Knox Gateway :          hchauhan-4.openstack.local
     
   #### Install Knox 
    #yum install knox -y
   Setup the Knox master secret 
       
       [knox@hchauhan-4 ~]$ /usr/hdp/current/knox-server/bin/gateway.sh setup
      Enter master secret:
      Enter master secret again:
   Start the Knox Gateway and Demo ldap 
      
      [root@hchauhan-4 hbase]# su -l knox -c "/usr/hdp/current/knox-server/bin/gateway.sh start"
    Starting Gateway succeeded with PID 26341.
    [root@hchauhan-4 hbase]# su -l knox -c "/usr/hdp/current/knox-server/bin/ldap.sh start"
    Starting LDAP succeeded with PID 26426.
    [root@hchauhan-4 hbase]#
    
   #### Test Knox 
   Edit the file /etc/knox/conf/topologies/sandbox.xml 
    
    <service>
        <role>NAMENODE</role>
        <url>hdfs://hchauhan-1.openstack.local:8020</url>
    </service>
   Try running the curl call to get the Listing from WeBHDFS. 
   
    curl -k -u guest:guest-password -X GET "https://$gateway_host:8443/gateway/sandbox/webhdfs/v1/?op=LISTSTATUS"
    
    
# Task (Just one)
## Install Ranger and Hive, enable Ranger Plugin for Services.
#### Ref :- https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.6.5/bk_command-line-installation/content/ch_getting_ready_chapter.html
