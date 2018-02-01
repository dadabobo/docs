## zookeeper

#### ZooKeeper Commands: The Four Letter Words
* `conf`
  Print details about serving configuration.
* `cons`
  List full connection/session details for all clients connected to this server. Includes information on numbers of packets received/sent, session id, operation latencies, last operation performed, etc...
* `crst`
  Reset connection/session statistics for all connections.
* `dump`
  Lists the outstanding sessions and ephemeral nodes. This only works on the leader.
* `envi`
  Print details about serving environment
* `ruok`
  Tests if server is running in a non-error state. The server will respond with imok if it is running. Otherwise it will not respond at all.
  A response of "imok" does not necessarily indicate that the server has joined the quorum, just that the server process is active and bound to the specified client port. Use "stat" for details on state wrt quorum and client connection information.
* `srst`
  Reset server statistics.
* `srvr`
  Lists full details for the server.
* `stat`
  Lists brief details for the server and connected clients.
* `wchs`
  Lists brief information on watches for the server.
* `wchc`
  Lists detailed information on watches for the server, by session. This outputs a list of sessions(connections) with associated watches (paths). Note, depending on the number of watches this operation may be expensive (ie impact server performance), use it carefully.
* `wchp`
  Lists detailed information on watches for the server, by path. This outputs a list of paths (znodes) with associated sessions. Note, depending on the number of watches this operation may be expensive (ie impact server performance), use it carefully.
* `mntr`
  Outputs a list of variables that could be used for monitoring the health of the cluster.


### zookeeper 配置详解
配置文件
* `$(ZOOKEEPER_HOME)/conf/zoo.cfg`
* `$(ZOOKEEPER_HOME)/conf/log4j.properties`
* `$(dataDir)/myid`

配置文件 `$(ZOOKEEPER_HOME)/conf/zoo.cfg` 说明
* `clientPort`  
  客户端连接server的端口，即对外服务端口，一般设置为`2181`吧。
* `dataDir`  
  存储快照文件snapshot的目录。默认情况下，事务日志也会存储在这里。建议同时配置参数`dataLogDir`, 事务日志的写性能直接影响zk性能。
* `tickTime`  
  ZK中的一个时间单元。ZK中所有时间都是以这个时间单元为基础，进行整数倍配置的。例如，session的最小超时时间是`2*tickTime`。
* `dataLogDir`  
  事务日志输出目录。尽量给事务日志的输出配置单独的磁盘或是挂载点，这将极大的提升ZK性能。  
* `globalOutstandingLimit`  
  最大请求堆积数。默认是`1000`。ZK运行的时候， 尽管server已经没有空闲来处理更多的客户端请求了，但是还是允许客户端将请求提交到服务器上来，以提高吞吐性能。当然，为了防止Server内存溢出，这个请求堆积数还是需要限制下的。  
  (Java system property:`zookeeper.globalOutstandingLimit`. )
* `preAllocSize`  
  预先开辟磁盘空间，用于后续写入事务日志。默认是`64M`，每个事务日志大小就是`64M`。如果ZK的快照频率较大的话，建议适当减小这个参数。  
  (Java system property:`zookeeper.preAllocSize` )
* `snapCount`  
  每进行`snapCount`次事务日志输出后，触发一次快照(snapshot), 此时，ZK会生成一个`snapshot.*`文件，同时创建一个新的事务日志文件`log.*`。默认是`100000`.（真正的代码实现中，会进行一定的随机数处理，以避免所有服务器在同一时间进行快照而影响性能）  
  (Java system property:`zookeeper.snapCount`)
* `traceFile`  
  用于记录所有请求的log，一般调试过程中可以使用，但是生产环境不建议使用，会严重影响性能。  
  (Java system property:? `requestTraceFile` )
* `maxClientCnxns`
  单个客户端与单台服务器之间的连接数的限制，是ip级别的，默认是60，如果设置为0，那么表明不作任何限制。请注意这个限制的使用范围，仅仅是单台客户端机器与单台ZK服务器之间的连接数限制，不是针对指定客户端IP，也不是ZK集群的连接数限制，也不是单台ZK对所有客户端的连接数限制。指定客户端IP的限制策略，这里有一个patch，可以尝试一下：http://rdc.taobao.com/team/jm/archives/1334
* `clientPortAddress`  
  对于多网卡的机器，可以为每个IP指定不同的监听端口。默认情况是所有IP都监听 `clientPort` 指定的端口。
* `minSessionTimeoutmaxSessionTimeout`  
  Session超时时间限制，如果客户端设置的超时时间不在这个范围，那么会被强制设置为最大或最小时间。默认的Session超时时间是在`2 * tickTime` ~ `20 * tickTime` 这个范围
* `fsync.warningthresholdms`
  事务日志输出时，如果调用fsync方法超过指定的超时时间，那么会在日志中输出警告信息。默认是1000ms。
  (Java system property: `fsync.warningthresholdms`)
* `autopurge.purgeInterval`
在上文中已经提到，3.4.0及之后版本，ZK提供了自动清理事务日志和快照文件的功能，这个参数指定了清理频率，单位是小时，需要配置一个1或更大的整数，默认是0，表示不开启自动清理功能。(No Java system property)  New in 3.4.0
* `autopurge.snapRetainCount`
  这个参数和上面的参数搭配使用，这个参数指定了需要保留的文件数目。默认是保留3个。
* `electionAlg`
  在之前的版本中， 这个参数配置是允许我们选择leader选举算法，但是由于在以后的版本中，只会留下一种“TCP-based version of fast leader election”算法，所以这个参数目前看来没有用了，这里也不详细展开说了。
* `initLimit`
  Follower在启动过程中，会从Leader同步所有最新数据，然后确定自己能够对外服务的起始状态。Leader允许`F`在 `initLimit` 时间内完成这个工作。通常情况下，我们不用太在意这个参数的设置。如果ZK集群的数据量确实很大了，`F`在启动的时候，从Leader上同步数据的时间也会相应变长，因此在这种情况下，有必要适当调大这个参数了。
* `syncLimit`
  在运行过程中，Leader负责与ZK集群中所有机器进行通信，例如通过一些心跳检测机制，来检测机器的存活状态。如果`L`发出心跳包在`syncLimit`之后，还没有从`F`那里收到响应，那么就认为这个`F`已经不在线了。注意：不要把这个参数设置得过大，否则可能会掩盖一些问题。
* `leaderServes`
  默认情况下，Leader是会接受客户端连接，并提供正常的读写服务。但是，如果你想让Leader专注于集群中机器的协调，那么可以将这个参数设置为`no`，这样一来，会大大提高写操作的性能。
  (Java system property: `zookeeper.leaderServes`)。
* `server.x=[hostname]:nnnnn[:nnnnn]`
  这里的`x`是一个数字，与`myid`文件中的`id`是一致的。右边可以配置两个端口，第一个端口用于`F`和`L`之间的数据同步和其它通信，第二个端口用于Leader选举过程中投票通信。  
* `group.x=nnnnn[:nnnnn]weight.x=nnnnn`
  对机器分组和权重设置，可以参见[这里](http://zookeeper.apache.org/doc/r3.4.3/zookeeperHierarchicalQuorums.html)
* `cnxTimeout`  
  Leader选举过程中，打开一次连接的超时时间，默认是`5s`。
  (Java system property: `zookeeper.cnxTimeout`)
* `zookeeper.DigestAuthenticationProvider.superDigest`
  ZK权限设置相关，具体参见  《使用super身份对有权限的节点进行操作》和《ZooKeeper权限控制》
* `skipACL`  
  对所有客户端请求都不作ACL检查。如果之前节点上设置有权限限制，一旦服务器上打开这个开头，那么也将失效。
  (Java system property:  `zookeeper.skipACL` )
* `forceSync`  
  这个参数确定了是否需要在事务日志提交的时候调用 FileChannel.force来保证数据完全同步到磁盘。(Java system property: `zookeeper.forceSync` )
* `jute.maxbuffer`
  每个节点最大数据量，是默认是`1M`。这个限制必须在server和client端都进行设置才会生效。
  (Java system property: `jute.maxbuffer` )

`$(ZOOKEEPER_HOME)/conf/zoo.cfg` 示例
```bash
# file: wangwg2.zookeeper/templates/zoo.cfg
tickTime=2000               ## zk中的时间单元，单位毫秒。
initLimit=10                ## 初始化同步时间
syncLimit=5                 ## 心跳同步确认时间间隔
dataDir={{zk_data}}         ## 存储快照文件目录
clientPort=2181             ## 用于客户端连接的端口
autopurge.purgeInterval=24  ## 自动清理事务日志和快照文件的频率，单位小时
autopurge.snapRetainCount=3 ## 自动清理需要保留的文件数目

# 不是集群模式注释掉
server.1=192.168.99.31:2888:3888
server.2=192.168.99.32:2888:3888
server.3=192.168.99.32:2888:3888
```

### ZooKeeper：分布式应用程序的分布式协调服务
ZooKeeper是分布式，开放源码的分布式应用协调服务。它暴露了一组简单的原语，分布式应用程序可以基于它实现更高级别的服务如：同步，配置维护以及组和命名。它被设计为易于编程，并使用类似文件系统目录树结构的数据模型设计。它运行在Java中，并且具有Java和C的绑定。

协调服务非常难以搞定。它们特别容易出现诸如竞争条件和死锁等错误。ZooKeeper背后的动机是缓解分布式应用程序从头开始实现协调服务的责任。

#### 设计目标
**ZooKeeper很简单**
* ZooKeeper允许分布式进程通过与标准文件系统类似的共享分层命名空间相互协调。名称空间由ZooKeeper术语中的数据寄存器（称为`znodes`）组成，这些类似于文件和目录。与专为存储设计的典型文件系统不同，ZooKeeper数据保存在内存中，这意味着ZooKeeper可以实现高吞吐量和低延迟量。
* ZooKeeper实现对高性能，高可用性，严格有序的访问非常重视。ZooKeeper的性能方面意味着它可以在大型分布式系统中使用。可靠性方面使其不会成为单点故障。严格的排序意味着复杂的同步原语可以在客户端实现。

**ZooKeeper复制**
* 像它所协调的分布式进程一样，ZooKeeper本身就是为了在被称为 `“ensemble`” 的一组主机上复制。
* 构成ZooKeeper服务的服务器必须彼此了解。它们维护状态的内存映像，以及持久存储中的事务日志和快照。只要大部分服务器可用，ZooKeeper服务将可用。
* 客户端连接到单个ZooKeeper服务器。客户端维护TCP连接，通过它发送请求，获取响应，获取观看事件并发送心跳。如果与服务器的TCP连接断开，客户端将连接到不同的服务器。

**ZooKeeper顺序**
ZooKeeper使用反映所有ZooKeeper交易顺序的数字来标记每个更新。后续操作可以使用该命令来实现更高级别的抽象，例如同步原语。

**ZooKeeper很快**
它在“读主导”工作负载中特别快。ZooKeeper应用程序在数千台机器上运行，并且在读次数比写入次数更常见的情况下，性能最好，比例大约为10：1。

#### 数据模型和层次命名空间
ZooKeeper提供的名称空间与标准文件系统类似。名称是以斜杠（`/`）分隔的路径元素序列。ZooKeeper的名称空间中的每个节点都由路径标识。

#### 节点和短暂节点
与标准文件系统不同，ZooKeeper命名空间中的每个节点都可以拥有数据并作为孩子与它相关联。就像有一个允许文件也是目录的文件系统。（ZooKeeper设计用于存储协调数据：状态信息，配置，位置信息等，因此存储在每个节点上的数据通常很小，在字节到千字节范围）。我们使用术语 “`znode`” 来说明我们正在谈论ZooKeeper数据节点。

`Znodes` 维护一个统计结构，其中包括数据更改，ACL更改和时间戳的版本号，以允许缓存验证和协调更新。每次 `znode` 的数据发生变化时，版本号都会增加。例如，每当客户机检索数据时，它也会收到数据的版本。

存储在命名空间中每个`znode`处的数据以原子方式读取和写入。读取获取与`znode`相关联的所有数据字节，写入替换所有数据。每个节点都有一个访问控制列表（ACL），它限制谁能做什么。

ZooKeeper还具有短暂节点的概念。只要创建`znode`的会话处于活动状态，就会存在这些znodes。当会话结束时，`znode`被删除。当您要实现[tbd]时，临时节点很有用。

#### 条件更新和监视(`watches`)
ZooKeeper支持 “`watches`” 的概念。客户端可以在`znode`上设置 `watch`。当`znode`更改时，`watch` 将被触发并移除。当`watch`触发时，客户端收到一个数据包，说明`znode`已经改变了。如果客户端与Zoo Keeper服务器之间的连接断开，客户端将收到本地通知。这些可以用于[tbd]。

#### 保证
ZooKeeper非常快速，非常简单。由于其目标是为建设更为复杂的服务（如同步）提供一套保证的基础。这些是：
* 顺序一致性 - 按照客户端的更新顺序进行发送。
* 原子性 - 更新成功或失败。没有部分结果。
* 单系统映像 - 客户端将看到服务的相同视图，无论其连接到的服务器如何。
* 可靠性 - 一旦应用更新，它将从该时间开始持续到客户端覆盖更新。
* 及时性 - 系统的客户端视图在一定时间内保证是最新的。

有关这些的更多信息，以及如何使用它们，请参阅 [tbd]

#### 简单的API
ZooKeeper的设计目标之一是提供一个非常简单的编程接口。因此，它仅支持以下操作：
* 创建  
  在树中的某个位置创建一个节点
* 删除  
  删除一个节点
* 存在
  测试一个节点是否存在于某个位置
* 获取数据  
  从节点读取数据
* 设置数据
  将数据写入节点
* 得到孩子
  检索节点的子节点列表
* 同步
  等待数据传播

有关这些更深入的讨论，以及如何用于实施更高层次的运作，请参考 [tbd]

#### 实现
ZooKeeper组件显示ZooKeeper服务的高级组件。除了请求处理器之外，组成ZooKeeper服务的每个服务器都会复制其每个组件的自己的副本。

复制数据库是包含整个数据树的内存数据库。将更新记录到磁盘以获取可恢复性，并将写入序列化到磁盘，然后再应用到内存数据库。

每个ZooKeeper服务器都为客户端服务。客户端连接到一个服务器以提交`irequests`。从每个服务器数据库的本地副本处理读取请求。更改服务状态，写入请求的请求由契约协议进行处理。

作为契约协议的一部分，客户端的所有写入请求都将转发到单个服务器，称为 `leader`。称为`followers`的其他ZooKeeper服务器 从`leader`接收消息提议，并同意消息传递。消息传递层负责替代失败的`leader`，并与`leader`同步`followers`。

ZooKeeper使用自定义的原子消息协议。由于消息传递层是原子的，所以ZooKeeper可以保证本地副本不会发散。当`leader`收到写请求时，它会计算要应用写入时系统的状态，并将其转换为捕获此新状态的事务。

#### 用途
ZooKeeper的编程界面故意简单。然而，使用它可以实现更高阶的操作，例如同步原语，组成员资格，所有权等。一些分布式应用程序已经使用它：[tbd：从白皮书和视频演示中添加使用。）有关详细信息，请参阅 [TBD]
