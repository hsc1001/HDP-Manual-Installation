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
We are going to have the zookeeper services on 3 nodes of the cluster.
        hchauhan-1.openstack.local
        # hchauhan-2.openstack.local
        # hchauhan-3.openstack.local
