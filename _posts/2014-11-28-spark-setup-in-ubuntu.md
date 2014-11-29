---
layout: post
title: "Setting up standalone Spark on ubuntu(With Docker Image Provided)"
description: ""
category: 
tags: []
---

[Spark](https://spark.apache.org/) is a computational framework quite popular recently. Compared with Hadoop, it offers much nicer abstractions(e.g. Resilient Distributed Datasets). Here's how to configure a standalone-mode spark instance on ubuntu(I use 12.04). If you don't have the patience and just want to play, you can download it from [here](https://registry.hub.docker.com/u/firstprayer/big-data-related/) and follow the intruction at the end of this post.

### 1. Set up Java
In this case Oracle JDK is better than OpenJDK. To install Oracle SDK, first run:
	
{% highlight sh %}
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
{% endhighlight %}

Then install the desired version of JDK, I used JDK 7:

{% highlight sh %}
	sudo apt-get install oracle-java7-installer
{% endhighlight %}

If you have multiple installations of Java, you can run this command to choose the one you want to use:

{% highlight sh %}
	sudo update-alternatives --config java
{% endhighlight %}

### 2. Set up Hadoop(Optional)

We can setup hadoop and ask spark to read file from hdfs in later section. However this is optional because you can also specify a local path in spark shell. Still, this is a tutorial so I try to be as comprehensive as possible. This section talks about configuring a hadoop instance(also in standalone mode). You can also refer to [here](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html) for the configuration of the newest version hadoop and skip this part(though in my configuration the procedure is a little different).

First download hadoop from [official site](http://hadoop.apache.org/releases.html). At this times the newest is 2.5.2, download it. Note that in spark's official site it only gives pre-compiled version compatible with hadoop up to 2.4, but the one works with 2.4 can also work with 2.5 after testing. Extract the tar file to a local directory, say, /usr/local/hadoop

1.Make sure your system has ssh and rsync:

{% highlight sh %}
sudo apt-get install ssh
sudo apt-get install rsync
{% endhighlight %}

2.Configure environment variables:
		
{% highlight sh %}
# set to the root of your Java installation, mine is /usr/lib/jvm/java-7-oracle/
export JAVA_HOME={Your java home} 
# Assuming your installation directory is /usr/local/hadoop
export HADOOP_PREFIX=/usr/local/hadoop 
{% endhighlight %}

3.Modify configuration file:

	etc/hadoop/core-site.xml:

{% highlight xml %}
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost:9000</value>
	</property>
</configuration>
{% endhighlight %}
		
	etc/hadoop/hdfs-site.xml:

{% highlight xml %}
<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
</configuration>
{% endhighlight %}

check that you can ssh to the localhost without a passphrase: 
{% highlight sh %}
ssh localhost
{% endhighlight %}

If you cannot ssh to localhost without a passphrase, execute the following commands:

{% highlight sh %}
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
{% endhighlight %}

4.Last step: start hadoop
{% highlight sh %}
bin/hdfs namenode -format   # Format the filesystem
sbin/start-dfs.sh           # Start daemons
{% endhighlight %}

Here you might ran into the same problem while starting dfs as I did: it shows JAVA_HOME not found. To fix this, edit libexec/hadoop-config.sh, and add the following line at the top:

{% highlight sh %}
export JAVA_HOME={Your java home}
{% endhighlight %}


### 3. Finally, spark!

Download spark from [here](http://spark.apache.org/downloads.html), choose pre-built version of hadoop 2.4. Extract to a local directory.

Before we actually start to play with spark, let's download some text file, say, [this page](http://en.wikipedia.org/wiki/Apache_Spark), and put it in hdfs as <code>/user/(Your user name)/input/Apache_Spark</code>. You can also just leave it on the hard-drive, and use its absolute path when reading the file.

Run the spark shell, exectue:

{% highlight sh %}
scala> val textFile = sc.textFile("hdfs://localhost:9000/user/(username)/input/Apache_Spark")
scala> textFile.count()
res3: Long=628

scala> textFile.first()
res4: String = <!DOCTYPE html>
{% endhighlight %}

Now let's do the classic one-line word count: 

{% highlight sh %}
scala> val wordCount = textFile.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey((v1, v2) => v1 + v2)

res5: Array[(String, Int)] = Array(("                       </h3>",1), (class="toclevel-2,3), (learning</a>,1), (href="/wiki/Apache_Flex",1), (agree,1), (title="Help:Category">Categories</a>:,1), (src="//upload.wikimedia.org/wikipedia/en/thumb/4/48/Folder_Hexagonal_Icon.svg/16px-Folder_Hexagonal_Icon.svg.png",1), (Ameet,2), (href="http://www.wired.com/wiredenterprise/2013/06/yahoo-amazon-amplab-spark/all/">"Spark:,1), (Commons">Commons,1), (Inline-Template,1), (Highest,1), (0px,2), (action="/w/index.php",1), (Hive</a>.</li>,1), (been,1), (id="p-variants-label"><span>Variants</span><a,1), (id="Disk_Based">Disk,1), (href="/wiki/Databricks",1), (Top-Level,2), (href="#cite_ref-5">^</a></b></span>,1), (title="Wikipedia,2), (href="#cite_ref-16">^</a></b></span>,1), (Incubator</a></span></li>,1), (Solr">Solr</...
{% endhighlight %}

Just a random example. There're many operators supported by spark(actually it's a super set of that of hadoop) and can be found in the official documentation.

Finally, here's a docker image with everything configured. You can download it from [here](https://registry.hub.docker.com/u/firstprayer/big-data-related/). Execute <code>/root/start-hadoop.sh</code> first. Spark is in the directory of <code>/root/spark</code>


