---
layout: post
title: "Setting up standalone Spark on ubuntu"
description: ""
category: 
tags: []
published: false
---

[Spark] is a computational framework quite popular recently. Compared with Hadoop, it offers much nicer abstractions(e.g. Resilient Distributed Datasets). Here's how to configure a standalone-mode spark instance on ubuntu(I use 12.04).

### 1. Set up Java
In this case Oracle JDK is better than OpenJDK. To install Oracle SDK, first run:

    sudo apt-get install python-software-properties
    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update

Then install the desired version of JDK, I used JDK 7:

    sudo apt-get install oracle-java7-installer

If you have multiple installation of Java, you can run this command to choose the one you want to use:

    sudo update-alternatives --config java

### 2. Set up Hadoop(Optional)

We can setup hadoop and ask spark to read file from hdfs in later section. However this is optional because you can also specify a local path in spark shell. Still, this is a tutorial so I try to be as comprehensive as possible. This section talks about configuring a hadoop instance(also in standalone mode). You can also refer to [here](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html) for the configuration of the newest version hadoop and skip this part(though in my configuration the procedure is a little different).

First download hadoop from [official site](http://hadoop.apache.org/releases.html). At this times the newest is 2.5.2, download it. Note that in spark's official site it only gives pre-compiled version compatible with hadoop up to 2.4, but the one works with 2.4 can also work with 2.5 after testing. Extract the tar file to a local directory, say, /usr/local/hadoop

1.  Make sure your system has ssh and rsync:  
        sudo apt-get install ssh
        sudo apt-get install rsync
2.  Configure environment variables:
        
        # set to the root of your Java installation
        export JAVA_HOME={Your java home} # Mine is /usr/lib/jvm/java-7-oracle/

        # Assuming your installation directory is /usr/local/hadoop
        export HADOOP_PREFIX=/usr/local/hadoop

3.  Modify configuration file:

    etc/hadoop/core-site.xml:

        <configuration>
            <property>
                <name>fs.defaultFS</name>
                <value>hdfs://localhost:9000</value>
            </property>
        </configuration>
        
    etc/hadoop/hdfs-site.xml:

        <configuration>
            <property>
                <name>dfs.replication</name>
                <value>1</value>
            </property>
        </configuration>

    check that you can ssh to the localhost without a passphrase: 
        ssh localhost

    If you cannot ssh to localhost without a passphrase, execute the following commands:

        $ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
        $ cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys

4.  Last step: start hadoop
    
        bin/hdfs namenode -format   # Format the filesystem
        sbin/start-dfs.sh           # Start daemons

    Here you might ran into the same problem while starting dfs as I did: it shows JAVA_HOME not found. To fix this, edit libexec/hadoop-config.sh, and add the following line at the top:

        export JAVA_HOME={Your java home}

### 3. Finally, spark!

Download spark from [here](http://spark.apache.org/downloads.html), choose pre-built version of hadoop 2.4. Extract to a local directory.

Now, you should be able to run the spark shell by simply executing <code>bin/spark-shell</code>. If not, maybe you need to check your installation of hadoop first. 

Before we actually start to play with spark, let's download some text file, say, [this page](http://en.wikipedia.org/wiki/Apache_Spark), and put it in hdfs as /user/(Your user name)/input/Apache_Spark.

Run the spark shell, exectue:

    scala> val textFile = sc.textFile("hdfs://localhost:9000/user/(username)/input/Apache_Spark")
    scala> textFile.count()
    res3: Long=628

    scala> textFile.first()
    res4: String = <!DOCTYPE html>

Now let's do the classic one-line word count



