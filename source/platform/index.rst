平台搭建
=========================

.. toctree::

   Cmake.rst
   AndroidStudio.rst
   XCode.rst
   web_program.rst
   react_native.rst

Docker
-------
`教程 <https://yeasy.gitbooks.io/docker_practice/content/image/dockerfile/copy.html>`_

1 安装
^^^^^^^^
参照官网，安装完后必须要 ``sudo`` , 通过以下方法解决

.. code-block:: bash

   sudo groupadd docker
   sudo usermod -aG docker $USER
   newgrp - docker

2 常用命令
^^^^^^^^^^^^

.. code-block:: bash

   docker pull [选项] [Docker Registry地址]<仓库名>:<标签>  # 获取镜像, 下载的镜像文件存放在本地 
                                                            # ubuntu: /var/lib/docker/graph/<image id>
                                                            # mac: /Users/Emily/Library/Containers/com.docker.docker/Data
   # --help  帮助

   docker run -it --rm ubuntu:14.04 bash  # 运行
   # -it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。
   # --rm：容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。
   # --name:
   # -d: 容器会在后台运行, 并不会把输出的结果(STDOUT)打印到宿主机上面(输出结果可以用docker logs 查看)
   # -p <宿主端口>:<容器端口>
   # -v: volume (卷名:挂载路径, 必须是绝对路径。如果是相对路径，实际挂载的是 /var/lib/docker/volumes/ 下的文件夹)
   # -e: 设置环境变量

   docker start # 将一个已经终止的容器启动运行
   docker stop  # 终止一个运行中的容器
   docker restart # 将一个运行态的容器终止，然后再重新启动它
   docker attach --sig-proxy=false $CONTAINER_ID  # 进入正在运行的容器，--sig-proxy=false确保CTRL-D或CTRL-C不会关闭容器
   docker exec # runs a new command in a running container.

   docker images   # 列出镜像
   # -f: 过滤
   # -a: 显示包括中间层镜像在内的所有镜像
   # dangling=true  # 虚悬
   # since: 之后
   # before: 之前
   # -q: 输出格式为ID 列表
   # --format: Go语法指定输出格式

   docker inspect  # 查看开启容器的相关信息

   
   docker ps # 查看容器
   # -a: 包括终止状态的容器

   docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]  # 保存为镜像
   # --author
   # --message
 
   docker history  # 具体查看镜像内的历史记录

   docker build [选项] <上下文路径/URL/->  # 镜像构建
   # -t 指定最终镜像的名称
   # -f 指定dockerfile 

   docker rm # 删除一个处于终止状态的容器
   # $(docker ps -a -q)  # 删除所有处于终止状态的容器
   # -v # 删除容器的同时删除挂载的数据卷，推荐
   docker rmi # 删除镜像

   docker login # 登录, 本地用户目录的 .dockercfg 中将保存用户的认证信息
   # -u：用户名
   # -p: 密码

   docker search # 搜索，无需登录
   # -s N: 指定仅显示评价为 N 星以上的镜像

   docker tag <本地repo>:<tag> <用户名>/<repo>:<tag>
   docker push <用户名>/<repo>:<tag>   # 镜像推送

3 Dockerfile语法
^^^^^^^^^^^^^^^^^

.. Note::
        Dockerfile 中每一个指令都会建立一层, Union FS 是有最大层数限制的，比如 AUFS，曾经是最大不得超过 42 层，现在是不得超过 127 层。
        因此镜像构建时要注意层数, 一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。

FROM 指定基础镜像

RUN  执行命令行命令
  - shell 格式：RUN <命令>
  - exec 格式：RUN ["可执行文件", "参数1", "参数2"]

COPY 复制
  - shell 格式：COPY <源路径>... <目标路径>
  - exec 格式：COPY ["<源路径1>",... "<目标路径>"]""]

CMD 容器启动命令
  - shell 格式：CMD <命令>
  - exec 格式：CMD ["可执行文件", "参数1", "参数2"...]""]

ENTRYPOINT 入口点

VOLUME 定义匿名卷(注意是定义而不是挂载，Dockerfile中不能挂载)
  - VOLUME ["<路径1>", "<路径2>"...]
  - VOLUME <路径>

4 搭建私有仓库
^^^^^^^^^^^^^^^

在机器A
""""""""""

- 安装 registry

  .. code-block:: bash

      docker pull registry

- 运行本地的 registry 

  .. code-block:: bash

      docker run -d -p 5000:5000 --restart=always --name registry -v <your path to registry-images>:/var/lib/registry registry:latest

  .. Note::
      registry 的默认使用的容器内目录可以通过 docker inspect <container name/ID> 查看 Mounts/volume 项目查看

此时在本机浏览器输入 `127.0.0.1:5000/v2/_catalog` 或在局域网的另一台机子输入 `xxx.xxx.xxx.xxx:5000/v2/_catalog` 确认服务是否正常开启


在机器A/B
""""""""""

.. Note:: 
        <tag> 很重要，一定要加，否则容易出错

- 上传

  .. code-block:: bash
  
      docker tag <local images> <repo ip>:<port>/<repo name>:<tag>     
      docker push <repo ip>:<port>/<repo name>:<tag>

- 下载

  .. code-block:: bash

      docker pull <repo ip>:<port>/<repo name>:<tag>

管理私有仓库
""""""""""""""

  .. code-block:: bash

      curl -XGET http://registry:5000/v2/_catalog
      curl -XGET http://registry:5000/v2/image_name/tags/list

可能出现问题
""""""""""""""

   在局域网通信过程中，必须做以下设置
   
   - 修改主机 A 的 docker 配置（/etc/docker/daemon.json 文件不存在则直接创建) ::

           {    
               "insecure-registries": ["xxx.xxx.xxx.xxx:5000"]}
           }

     .. code-block:: bash 
         
         sudo systemctl daemon-reload    
         sudo systemctl restart docker

   
   - 修改主机 B 的 docker 配置（/etc/docker/daemon.json 文件不存在则直接创建) ::

           {    
               "registry-mirrors": ["192.168.0.111:5000"],    // 注意这里有个逗号
               "insecure-registries": ["192.168.0.111:5000"]
           }
     
     .. code-block:: bash
      
        sudo systemctl daemon-reload    
        sudo systemctl restart docker  # 重启 docker 服务

   在mac 上直接在Preference/daemon 中修改即可

Hadoop
-------

Requirement
^^^^^^^^^^^^^^^

- java(jdk)

Setup Tutorial - Installation & Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`TODO1 <https://blog.csdn.net/hliq5399/article/details/78193113>`_

`TODO2 <http://www.cnblogs.com/qingyunzong/p/8524594.html>`_

0. 修改所有节点的 :file:`/etc/hosts` 文件

   将127.0.1.1 对应的行注释，另外添加节点主机的ip及名称::

       #127.0.1.1    xshine4-Inspiron-3668
       192.168.1.5  master
       192.168.1.113 slave1

   修改完后看几台机子是否可以ping通

   .. code-block:: bash

       ping slave1

1. 为hadoop集群专门设置一个用户组及用户 

   .. code-block:: bash

       sudo addgroup hadoop_
       sudo adduser --ingroup hadoop_ hduser_

       //为hdusr_增加管理员权限
       sudo adduser hduser_ sudo  // 或者 usermod -G sudo hdusr_

2. 设置ssh无密码登录子节点

   .. code-block:: bash

       su - hduser_  //switch user, 以下的操作都是在该用户下进行
       ssh-keygen -t rsa -P "" //create a new key.
       cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys  //Enable SSH access to local machine using this key.

       // Now test SSH setup by connecting to locahost as 'hduser_' user.
       ssh localhost

   .. important::
    
       步骤0至此需要在所有节点里面都同样进行。

   接下来设置ssh无密码登录子节点，假设主节点为master，子节点为slaven，分别在两个子节点的命令终端执行命令

   .. code-block:: bash

       cd ~/.ssh
       scp hduser_@master:~/.ssh/id_rsa.pub ./master_rsa.pub
       cat master_rsa.pub >> authorized_keys

   执行完上述命令后，回到主节点master，输入如下命令测试能否无密码登录

   .. code-block:: bash 
       
       ssh slaven // 此处默认主节点与子节点用户名一致，若不一致，需按 username@slaven 的格式连接

3. `下载Hadoop <http://www.apache.org/dyn/closer.cgi/hadoop/common/>`_

   选择stable版本下载, 解压并修改owner

   .. code-block:: bash  

       sudo chown -R hduser_:hadoop_ <hadoop dir>

4. 设置环境变量

   - 设置 :file:`~/.bashrc` 

       .. code::

           #Set HADOOP_HOME
           export HADOOP_HOME=<Installation Directory of Hadoop>
           #Set JAVA_HOME
           export JAVA_HOME=<Installation Directory of Java>
           # Add bin/ directory of Hadoop to PATH
           export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

   - 设置 :file:`$HADOOP_HOME/etc/hadoop/hadoop-env.sh` 文件中的 ``JAVA_HOME`` 变量。 

   此时可以通过以下命令查看java 和 hadoop 是否安装完成

   .. code-block:: bash

       java --version
       hadoop version

在 :file:`hadoop-x.x.x/etc/hadoop` 文件夹中，有5个配置文件 :file:`slaves` , :file:`core-site.xml` , :file:`hdfs-site.xml` , :file:`mapred-site.xml` , :file:`yarn-site.xml` 需要配置。

5. Configurations related to HDFS


   在 :file:`slaves` 中添加各主机的主机名::

       localhost  // 伪分布中使用，完全分布需删除
       slaven

   修改 :file:`core-site.xml` , Copy below line in between tags **<configuration>** and **</configuration>** ::

       <!-- 指定hadoop运行时产生文件的存储目录(伪) -->
       <property>
           <name>hadoop.tmp.dir</name>
           <value>/home/username/hadoop_home/tmp</value>  // 注意路径
           <description>Parent directory for other temporary directories.</description>
       </property>

       <!-- 指定HADOOP所使用的文件系统schema（URI），HDFS的老大（NameNode）的地址(伪) -->
       <property>
           <name>fs.defaultFS </name>
           <value>hdfs://master:9000</value>  // 注意主机名，不要用localhost，可以用主机ip, 否则可能出现connection refused的错误
           <description>The name of the default file system. </description>
       </property>

6. Map Reduce Configuration

   修改 :file:`hdfs-site.xml` , Add below lines of setting between tags **<configuration>** and **</configuration>** ::

       <!-- 设置secondarynamenode的http通讯地址 -->
       <!--property>
           <name>dfs.namenode.secondary.http-address</name>
           <value>master:50090</value>  //注意路径
       </property-->

       <!-- 指定HDFS副本的数量(伪) -->
       <property>
           <name>dfs.replication</name>
           <value>2</value>  //这个数字决定备份几份
           <description>Default block replication.</description>
       </property>
       
       <!-- (伪) -->
       <property>
           <name>dfs.namenode.name.dir</name>
           <value>/home/username/hadoop_home/tmp/dfs/name</value>  //注意路径
       </property>

       <!-- (伪) -->
       <property>
           <name>dfs.datanode.data.dir</name>
           <value>/home/username/hadoop_home/tmp/dfs/data</value>  //注意路径
       </property>

   修改 :file:`mapred-site.xml` , 如果没有这个文件，先执行

   .. code-block:: bash

       sudo cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml

   Add below lines of setting in between tags **<configuration>** and **</configuration>** ::

       <property>
           <name>mapreduce.jobhistory.address</name>
           <value>master:10020</value>
           <description>MapReduce job tracker runs at this host and port.
           </description>
       </property>

       <!-- 通知框架MR运行在yarn上(伪) -->
       <property>
           <name>mapreduce.framework.name</name>
           <value>yarn</value>
       </property>

   若前面配置了yarn，则配置 :file:`yarn-site.xml` ::

        <!-- Site specific YARN configuration properties -->
        <!-- 指定YARN的老大（ResourceManager）的地址 -->
        <property>
            <name>yarn.resourcemanager.address</name>
            <value>master:8032</value>
        </property>

        <!-- reducer获取数据的方式(伪) -->
        <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
        </property>

   .. important::

       步骤4～7 只在主节点master上执行, 子节点上直接拷贝相应的文件即可。
   
7. 子节点配置
   
   上面的hadoop-env.sh，core-site.xml，mapred-site.xml，hdfs-site.xml等几个文件，在四台linux中都是一样的。配置完一台电脑后，可以将hadoop包，直接拷贝到其他电脑上。将第4~6步配置的环境复制到子节点中。

8. 启动hadoop集群

   启动集群前，需先格式化 **主节点** namenode，只格式化一次。在主节点输入命令

   .. code-block:: bash

      hdfs namenode -format

   而后进入主节点master目录 :file:`~/<hadoop dir>` 下，执行
   
   .. code-block:: bash

       //启动/关闭NameNode 和 DataNode
       start-dfs.sh  
       stop-dfs.sh 

       //启动/关闭Yarn
       start-dfs.sh
       start-yarn.sh
       mr-jobhistory-daemon.sh start historyserver

       stop-dfs.sh
       stop-yarn.sh
       mr-jobhistory-daemon.sh stop historyserver

   启动命令后，输入jps命令，查看是否启动成功。主节点下应该出现以下项目::

       // 伪分布
       NodeManager
       NodeName
       DataNode
       ResourceManager
       Jps
       SecondaryNameNode

       // 完全分布
       NodeName
       ResourceManager
       Jps
       SecondaryNameNode

   子节点应该出现::

       DataNode
       Jps
       NodeManager


9. 可视化
   
   可以通过主节点的浏览器访问http://localhost:50070 进入namenode 的管理界面, 访问8088 进入resourceManager的管理界面。

10. 测试hadoop集群

   在主节点的hadoop 目录下执行

   .. code-block:: bash

       hdfs dfs -mkdir -p /data/input
       vim my_wordcount.txt
       hdfs dfs -put my_wordcount.txt /data/input
       hdfs dfs -ls /data/input

       hadoop jar share/hadoop/mapreduce/hadoop-mapeduce-examples-2.9.1.jar wordcount /data/input/my_wordcount.txt /data/output/my_wordcount
       hdfs dfs -cat /data/output/my_wordcount/part-r-00000

错误集锦
^^^^^^^^^^^

1. DataNode没启动

   原因
       当我们使用hadoop namenode -format格式化namenode时，会在namenode数据文件夹（这个文件夹为自己配置文件中dfs.name.dir的路径）中保存一个current/VERSION文件，记录clusterID，datanode中保存的current/VERSION文件中的clustreID的值是上一次格式化保存的clusterID，这样，datanode和namenode之间的ID不一致。

   解决：
       - 如果dfs文件夹中没有重要的数据，那么删除dfs文件夹，再重新运行格式化及启动命令： （删除所有节点下的dfs文件夹，dfs目录在${HADOOP_HOME}/tmp/）
       - 如果dfs文件中有重要的数据，那么在dfs/name目录下找到一个current/VERSION文件，记录clusterID并复制。然后dfs/data目录下找到一个current/VERSION文件，将其中clustreID的值替换成刚刚复制的clusterID的值即可；

Jenkins
--------

Install
^^^^^^^

- ubuntu :ref:`jenkins-install-ubuntu` 

Usage
^^^^^^

使用 ``systemctl`` 开启服务

.. code-block:: bash

   sudo systemctl start jenkins
   sudo systemctl status jenkins // 查看是否开启成功
   sudo systemctl stop jenkins // 停止服务

成功开启后，在浏览器中输入 ``http://localhost:8080`` 登录jenkins 账号，首次登录需要输入密码，密码位于 :file:`/var/lib/jenkins/secrets/initialAdminPassword`

按提示完成设置，安装成功后在命令行输入

.. code-block:: bash
     
   sudo netstat -plntu

查看

