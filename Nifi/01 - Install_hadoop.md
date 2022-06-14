# **Install hadoop**

Firts, we have to **install hadoop** because nifi need it to run. The steps are:

1.  Download the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.
    
        sudo apt update

2. See the java version.

        javac -version

3. Install openSSH client and server.

        sudo apt install openssh-server openssh-client -y

4.  Generate SSH authentication key.

        ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

5. Aggregate the SSH authentication key a authorized_keys

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

6. Give permissions (owner can read, can write and can't execute)

        chmod 0600 ~/.ssh/authorized_keys

7. Connect to host localhost
        
        ssh localhost

8. Download hadoop 2.10.2

        wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz

9. Unzip the downloaded package.

        tar xzf hadoop-2.10.2.tar.gz

10. Open .bashrc document and introduce the following properties.

    nano .bashrc

        export HADOOP_HOME=/home/spark/hadoop-2.10.2
        export HADOOP_INSTALL=$HADOOP_HOME
        export HADOOP_MAPRED_HOME=$HADOOP_HOME
        export HADOOP_COMMON_HOME=$HADOOP_HOME
        export HADOOP_HDFS_HOME=$HADOOP_HOME
        export YARN_HOME=$HADOOP_HOME
        export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
        export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
        export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/nativ"


11. Read and execute the content of .bashrc


        source ~/.bashrc

13. Change the JAVA_HOME variable with the value of java path in hadoop-env.sh document.

        which javac #To know the java path

        readlink -f /usr/bin/javac

        nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh

          JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"


14. Open core-site.xml document and introduce the following properties.

        nano $HADOOP_HOME/etc/hadoop/core-site.xml

        <configuration>
        <property>
          <name>fs.defaultFS</name>
          <value>hdfs://localhost:9000</value>
        </property>
        </configuration>


15.  Open hdfs-site.xml document and introduce the following properties.

    nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml

    <configuration>
    <property>
      <name>dfs.replication</name>
      <value>1</value>
    </property>
    <property>
      <name>dfs.namenode.name.dir</name>
      <value>/home/spark/datos/namenode</value>
    </property>
    <property>
      <name>dfs.datanode.data.dir</name>
      <value>/home/spark/datos/datanode</value>
    </property>
    </configuration>

16.  Create the namenode anda datanode folder.

    cd /home/spark/
    mkdir datos 
    mkdir datos/namenode
    mkdir datos/datanode

17. Open mapred-site.xml document and introduce the following properties.

        nano $HADOOP_HOME/etc/hadoop/mapred-site.xml

        <configuration>
        <property>
          <name>mapreduce.framework.name</name>
          <value>yarn</value>
        </property>
        </configuration>

18. Open yarn-site.xml document and introduce the following properties.

        nano $HADOOP_HOME/etc/hadoop/yarn-site.xml

        <configuration>
        <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value>
        </property>
        <property>
          <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
          <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>
        <property>
          <name>yarn.resourcemanager.hostname</name>
          <value>127.0.0.1</value>
        </property>
        <property>
          <name>yarn.acl.enable</name>
          <value>0</value>
        </property>
        <property>
          <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
        </property>
        </configuration>

19. Format 

        hdfs namenode -format

20. Run dfs and yard.

        cd ..
        cd hadoop-2.10.2/sbin/
        ./start-dfs.sh
        ./start-yarn.sh

21.  Check dfs and yard is running.

    jps

22. Open the port.  

        http://localhost:8088