
YARN：分布式资源管理框架；管理整个集群的资源（内存，CPU核数）；分配调度集群的资源。

Namenode是主节点，存储文件的元数据如文件名，文件的目录结构，文件属性（生成时间，副本数，文件权限），以及每个文件的块列表和块所在的DataNode等。

Datanode在本地文件系统存储文件块数据，以及块数据的校验和。

SecondaryNameNode用来监控HDFS状态的辅助后台程序，每个一段时间来获取HDFS元数据的快照（合并镜像文件的编辑日志）。

https://www.cnblogs.com/cxzdy/p/5494929.html

YARN的架构图很人性化，client表示客户端，当client提交任务的时候，它会提交到ResourceManager，而在YARN的整个系统中，RM管理着所有的NodeManager，举个简单的例子，将YARN每一次提交的任务比作一个项目，RM相当于经理，NM相当于每一个模块的负责组，比如前后端等模块，在NM中会有一个container和APP master（RM创建的应用管理者），container相当于一个资源容器，不同的容器装着不同的请求所需要的资源，APP master会向RM发出请求，申请资源，然后去对应的container去获取对应的资源。

![MR on YARN](https://github.com/ljcan/jqBlogs/blob/master/Hadoop/MR%20on%20YARN.png)

**MapReduce on YARN流程图简述：**

当客户端提交任务到ResourceManager，ApplicationManager会去管理NodeManager生成一个App Master（应用管理者），当App Master建立之后，就会向ApplicationsManager来反馈并且调度，向ResouceScheduler调度资源，当调度完毕以后,App Master就会去管理相应的Node Manager，到容器Container中调度相应的任务，Map Task或者Reduce Task，在每一个任务运行的过程中，App Master还会一直监控着每一个任务，因此每一个任务会向其反馈当前任务的运行信息，最后，任务运行完毕后向ApplicationsManager提交任务，client还可以通过页面的信息来通过App Master监控任务的当前状况。

**DataNode数据校验**

当DataNode读取block的时候，它会计算checksum，如果计算后的checksum与block创建时值不一样，说明该block已经损坏，client读取其他DataNode上的block，然后删除该block，并且复制block副本数达到预期设置的文件备份数。DataNode在其文件创建三周后验证其checksum。

**NameNode启动过程**

![namenode启动过程](https://github.com/ljcan/jqBlogs/blob/master/Hadoop/NameNode%E5%90%AF%E5%8A%A8%E8%BF%87%E7%A8%8B.png)

namenode：

namenode的数据存储在内存或者磁盘文件fsimage（格式化文件系统就是为了生成这个，存储着元数据）、edits中，在执行`start namenode`命令后，会read fsimage文件，在`start datanode`的时候，向namenode进行注册，并且进行block report，然后创建临时文件，并且写edits文件，在第二次启动namenode的时候，会读fsimage或者edits文件，并且生成新的fsimage文件。

**secondaryNameNode辅助功能**

namenode中的编辑日志文件如果太大的话需要借助secondaryNameNode，定期的合并，将fsimage文件和edits文件合并成一个新的fsimage文件，因为hdfs读取fsimage文件要比edits文件快很多。

### Flume

event是flume数据传输的基本单元，flume以事件的形式将数据从源头传送到最终的目的，event由可选的header和载有数据的一个byte array构成，载有数据对flume是不透明的，header是容纳了key-value字符串对的无序集合，key在集合内是唯一的，header也可以在上下文路由中使用扩展。

Flume-ng只有一个角色的节点，agent的角色，agent有source，channel，sink组成。





