## kafka

参考资料
* [Jason's Blog](http://www.jasongj.com/)
* [kafka](http://kafka.apache.org/)
* [Jafka](https://github.com/adyliu/jafka/)
* [Running a Multi-Broker Apache Kafka 0.8 Cluster on a Single Node](http://www.michael-noll.com/blog/2013/03/13/running-a-multi-broker-apache-kafka-cluster-on-a-single-node/)
* [Apache Storm and Kafka Cluster with Docker](http://alvinhenrick.com/2014/08/18/apache-storm-and-kafka-cluster-with-docker/)
* [alvinhenrick/log-kafka-storm](https://github.com/alvinhenrick/log-kafka-storm)
* [miguno/wirbelsturm](https://github.com/miguno/wirbelsturm)
* [Nexys-Technology/docker-kafka-storm-hdfs](https://github.com/Nexys-Technology/docker-kafka-storm-hdfs)
* [wipatrick/docker-kafka-storm](https://github.com/wipatrick/docker-kafka-storm)
* [jiekechoo/log-analysis-kafka-storm-docker](https://github.com/jiekechoo/log-analysis-kafka-storm-docker)
* [simplesteph/kafka-stack-docker-compose](https://github.com/simplesteph/kafka-stack-docker-compose)
* [sheepkiller/kafka-manager-docker](https://github.com/sheepkiller/kafka-manager-docker)
* [spiside/kafka-cluster](https://github.com/spiside/kafka-cluster)
* [wurstmeister/kafka-docker](https://github.com/wurstmeister/kafka-docker)
* [desp0916/Storm-Kafka-Docker](https://github.com/desp0916/Storm-Kafka-Docker)
* [sergiv83/kafka-storm-compose](https://github.com/sergiv83/kafka-storm-compose)
* [Ansible Playbook - Setup Kafka Cluster](https://github.com/zubayr/ansible_kafka_tarball)


### Kafka Quick Start
```bash
## 下载安装
tar -xzf kafka_2.11-1.0.0.tgz
cd kafka_2.11-1.0.0

## 启动 zookeeper 
bin/zookeeper-server-start.sh config/zookeeper.properties
## 启动 kafka
bin/kafka-server-start.sh config/server.properties
## 让我们用一个分区创建一个名为 "test" 的主题, 只有一个副本:
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
bin/kafka-topics.sh --list --zookeeper localhost:2181
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```



### kafka 配置

#### broker 的全局配置
**系统 相关**
* `broker.id = 1`
  每一个broker在集群中的唯一标示，要求是正数。在改变IP地址，不改变`broker.id`的话不会影响consumers
* `log.dirs = /data/kafka-d01`
  kafka数据的存放地址，多个地址的话用逗号分割 `/data/kafka-d01,/data/kafka-d02`
* `port = 6667`
  提供给客户端响应的端口
* `message.max.bytes = 1000000`
  消息体的最大大小，单位是字节
* `num.network.threads = 3`
  broker 处理消息的最大线程数，一般情况下不需要去修改
* `num.io.threads = 8`
  broker处理磁盘IO 的线程数 ，数值应该大于你的硬盘数
* `background.threads = 4`
  一些后台任务处理的线程数，例如过期消息文件的删除等，一般情况下不需要去做修改
* `queued.max.requests = 500`
  等待IO线程处理的请求队列最大数，若是等待IO的请求超过这个数值，那么会停止接受外部消息，算是一种自我保护机制
* `host.name`
  broker的主机地址，若是设置了，那么会绑定到这个地址上，若是没有，会绑定到所有的接口上，并将其中之一发送到ZK，一般不设置
* `advertised.host.name`
  打广告的地址，若是设置的话，会提供给producers, consumers,其他broker连接，具体如何使用还未深究
* `advertised.port`
  广告地址端口，必须不同于port中的设置
* `socket.send.buffer.bytes = 100 * 1024`
  socket的发送缓冲区，socket的调优参数SO_SNDBUFF
* `socket.receive.buffer.bytes = 100 * 1024`
  socket的接受缓冲区，socket的调优参数SO_RCVBUFF
* `socket.request.max.bytes = 100 * 1024 * 1024`
  socket请求的最大数值，防止serverOOM，`message.max.bytes`必然要小于`socket.request.max.bytes`，会被topic创建时的指定参数覆盖

**LOG 相关**  
* `log.segment.bytes = 1024 * 1024 * 1024`
  topic的分区是以一堆segment文件存储的，这个控制每个segment的大小，会被topic创建时的指定参数覆盖
* `log.roll.hours = 24*7`
  这个参数会在日志segment没有达到`log.segment.bytes`设置的大小，也会强制新建一个segment 会被 topic创建时的指定参数覆盖
* `log.cleanup.policy = delete`
  日志清理策略 选择有：delete和compact 主要针对过期数据的处理，或是日志文件达到限制的额度，会被 topic创建时的指定参数覆盖
* `log.retention.minutes=7 days`
  数据存储的最大时间 超过这个时间 会根据`log.cleanup.policy`设置的策略处理数据，也就是消费端能够多久去消费数据。
  `log.retention.bytes`和`log.retention.minutes`任意一个达到要求，都会执行删除，会被topic创建时的指定参数覆盖
* `log.retention.bytes=-1`
  topic每个分区的最大文件大小，`一个topic的大小限制 = 分区数*log.retention.bytes` 。-1 没有大小限制
  `log.retention.bytes`和`log.retention.minutes`任意一个达到要求，都会执行删除，会被topic创建时的指定参数覆盖
* `log.retention.check.interval.ms=5 minutes` 
  文件大小检查的周期时间，是否处罚 `log.cleanup.policy`中设置的策略
* `log.cleaner.enable=false`
  是否开启日志压缩
* `log.cleaner.threads =1`
  日志压缩运行的线程数
* `log.cleaner.io.max.bytes.per.second=None`
  日志压缩时候处理的最大大小
* `log.cleaner.dedupe.buffer.size=500*1024*1024`
  日志压缩去重时候的缓存空间 ，在空间允许的情况下，越大越好
* `log.cleaner.io.buffer.size=512*1024`
  日志清理时候用到的IO块大小 一般不需要修改
* `log.cleaner.io.buffer.load.factor = 0.9`
  日志清理中hash表的扩大因子 一般不需要修改
* `log.cleaner.backoff.ms =15000`
  检查是否处罚日志清理的间隔
* `log.cleaner.min.cleanable.ratio=0.5`
  日志清理的频率控制，越大意味着更高效的清理，同时会存在一些空间上的浪费，会被topic创建时的指定参数覆盖
* `log.cleaner.delete.retention.ms = 1 day`
  对于压缩的日志保留的最长时间，也是客户端消费消息的最长时间，同`log.retention.minutes`的区别在于一个控制未压缩数据，一个控制压缩后的数据。会被topic创建时的指定参数覆盖
* `log.index.size.max.bytes = 10 * 1024 * 1024`
  对于segment日志的索引文件大小限制，会被topic创建时的指定参数覆盖
* `log.index.interval.bytes = 4096`
  当执行一个fetch操作后，需要一定的空间来扫描最近的offset大小，设置越大，代表扫描速度越快，但是也更好内存，一般情况下不需要搭理这个参数
* `log.flush.interval.messages=None`
  log文件"sync"到磁盘之前累积的消息条数
  因为磁盘IO操作是一个慢操作,但又是一个"数据可靠性"的必要手段,所以此参数的设置,需要在"数据可靠性"与"性能"之间做必要的权衡.
  如果此值过大,将会导致每次"fsync"的时间较长(IO阻塞);如果此值过小,将会导致"fsync"的次数较多,这也意味着整体的client请求有一定的延迟.
  物理server故障,将会导致没有fsync的消息丢失.
* `log.flush.scheduler.interval.ms = 3000`
  检查是否需要固化到硬盘的时间间隔
* `log.flush.interval.ms = None`
  仅仅通过interval来控制消息的磁盘写入时机,是不足的.
  此参数用于控制"fsync"的时间间隔,如果消息量始终没有达到阀值,但是离上一次磁盘同步的时间间隔达到阀值,也将触发.
* `log.delete.delay.ms = 60000`
  文件在索引中清除后保留的时间 一般不需要去修改
* `log.flush.offset.checkpoint.interval.ms =60000`
  控制上次固化硬盘的时间点，以便于数据恢复 一般不需要去修改

**TOPIC 相关**  
* `auto.create.topics.enable =true`
  是否允许自动创建topic，若是false，就需要通过命令创建topic
* `default.replication.factor =1`
  一个topic ，默认分区的replication个数 ，不得大于集群中broker的个数
* `num.partitions = 1`
  每个topic的分区个数，若是在topic创建时候没有指定的话。会被topic创建时的指定参数覆盖
* 实例
  `--replication-factor 3 --partitions 1 --topic replicated-topic ：名称`
  replicated-topic有一个分区，分区被复制到三个broker上。
 
**复制(Leader、replicas) 相关**
* `controller.socket.timeout.ms = 30000`
  partition leader与replicas之间通讯时,socket的超时时间
* `controller.message.queue.size=10`
  partition leader与replicas数据同步时,消息的队列尺寸
* `replica.lag.time.max.ms = 10000`
  replicas响应partition leader的最长等待时间，若是超过这个时间，就将replicas列入ISR(in-sync replicas)，并认为它是死的，不会再加入管理中
* `replica.lag.max.messages = 4000`
  如果follower落后与leader太多,将会认为此follower[或者说partition relicas]已经失效。  
  通常,在follower与leader通讯时,因为网络延迟或者链接断开,总会导致replicas中消息同步滞后。
  如果消息之后太多,leader将认为此follower网络延迟较大或者消息吞吐能力有限,将会把此replicas迁移到其他follower中.
  在broker数量较少,或者网络不足的环境中,建议提高此值.
* `replica.socket.timeout.ms= 30 * 1000`
  follower与leader之间的socket超时时间
* `replica.socket.receive.buffer.bytes=64 * 1024`
  leader复制时候的socket缓存大小
* `replica.fetch.max.bytes = 1024 * 1024`
  replicas每次获取数据的最大大小
* `replica.fetch.wait.max.ms = 500`
  replicas同leader之间通信的最大等待时间，失败了会重试
* `replica.fetch.min.bytes =1`
  fetch的最小数据尺寸,如果leader中尚未同步的数据不足此值,将会阻塞,直到满足条件
* `num.replica.fetchers=1`
  leader 进行复制的线程数，增大这个数值会增加follower的IO
* `replica.high.watermark.checkpoint.interval.ms = 5000` 
  每个replica检查是否将最高水位进行固化的频率
* `controlled.shutdown.enable = false`
  是否允许控制器关闭broker ,若是设置为true,会关闭所有在这个broker上的leader，并转移到其他broker
* `controlled.shutdown.max.retries = 3`
  控制器关闭的尝试次数
* `controlled.shutdown.retry.backoff.ms = 5000`
  每次关闭尝试的时间间隔
* `auto.leader.rebalance.enable = false`
  是否自动平衡broker之间的分配策略
* `leader.imbalance.per.broker.percentage = 10`
  leader的不平衡比例，若是超过这个数值，会对分区进行重新的平衡
* `leader.imbalance.check.interval.seconds = 300`
  检查leader是否不平衡的时间间隔
* `offset.metadata.max.bytes`
  客户端保留offset信息的最大空间大小
 
**ZooKeeper 相关**
* `zookeeper.connect = localhost:2181`
  zookeeper集群的地址，可以是多个，多个之间用逗号分割 `hostname1:port1,hostname2:port2,hostname3:port3`
* `zookeeper.session.timeout.ms=6000`
  ZooKeeper的最大超时时间，就是心跳的间隔，若是没有反映，那么认为已经死了，不易过大
* `zookeeper.connection.timeout.ms = 6000`
  ZooKeeper的连接超时时间
* `zookeeper.sync.time.ms = 2000`
  ZooKeeper集群中leader和follower之间的同步实际那

* 配置的修改
  其中一部分配置是可以被每个topic自身的配置所代替，例如
  - 新增配置
    `bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic my-topic --partitions 1 --replication-factor 1 --config max.message.bytes=64000 --config flush.messages=1`
  - 修改配置
    `bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --config max.message.bytes=128000`
  - 删除配置
    `bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --deleteConfig max.message.bytes`

#### Consumer 配置
最为核心的配置是`group.id`、`zookeeper.connect`
* `group.id`
  Consumer归属的组ID，broker是根据`group.id`来判断是队列模式还是发布订阅模式，非常重要
* `consumer.id`
  消费者的ID，若是没有设置的话，会自增
* `client.id = group id value`
  一个用于跟踪调查的ID ，最好同`group.id`相同
* `zookeeper.connect=localhost:2182`
  对于zookeeper集群的指定，可以是多个 `hostname1:port1,hostname2:port2,hostname3:port3` 必须和broker使用同样的zk配置
* `zookeeper.session.timeout.ms = 6000`
  zookeeper的心跳超时时间，查过这个时间就认为是dead消费者
* `zookeeper.connection.timeout.ms = 6000`
  zookeeper的等待连接时间
* `zookeeper.sync.time.ms = 2000` 
  zookeeper的follower同leader的同步时间
* `auto.offset.reset = largest`
  当zookeeper中没有初始的offset时候的处理方式 。`smallest`：重置为最小值;  `largest`:重置为最大值; anything else：抛出异常
* `socket.timeout.ms= 30 * 1000`
  socket的超时时间，实际的超时时间是：`max.fetch.wait + socket.timeout.ms`.
* `socket.receive.buffer.bytes=64 * 1024`
  socket的接受缓存空间大小
* `fetch.message.max.bytes = 1024 * 1024` 
  从每个分区获取的消息大小限制
* `auto.commit.enable = true`
  是否在消费消息后将offset同步到zookeeper，当Consumer失败后就能从zookeeper获取最新的offset
* `auto.commit.interval.ms = 60 * 1000`
  自动提交的时间间隔
* `queued.max.message.chunks = 10`
  用来处理消费消息的块，每个块可以等同于`fetch.message.max.bytes`中数值
* `rebalance.max.retries = 4` 
  当有新的consumer加入到group时,将会reblance,此后将会有partitions的消费端迁移到新的consumer上,如果一个consumer获得了某个partition的消费权限,那么它将会向zk注册"Partition Owner registry"节点信息,但是有可能此时旧的consumer尚没有释放此节点,此值用于控制,注册节点的重试次数.
* `rebalance.backoff.ms = 2000`
  每次再平衡的时间间隔
* `refresh.leader.backoff.ms`
  每次重新选举leader的时间
* `fetch.min.bytes = 1`
  server发送到消费端的最小数据，若是不满足这个数值则会等待，知道满足数值要求
* `fetch.wait.max.ms = 100`
  若是不满足最小大小(`fetch.min.bytes`)的话，等待消费端请求的最长等待时间
* `consumer.timeout.ms = -1` 
  指定时间内没有消息到达就抛出异常，一般不需要改

#### Producer 的配置
比较核心的配置：`metadata.broker.list`、 `request.required.acks`、 `producer.type`、 `serializer.class`.
* `metadata.broker.list`
  消费者获取消息元信息(topics, partitions and replicas)的地址,配置格式是：`host1:port1,host2:port2`，也可以在外面设置一个vip
* `request.required.acks = 0` 
  消息的确认模式
  - `0`：不保证消息的到达确认，只管发送，低延迟但是会出现消息的丢失，在某个server失败的情况下，有点像TCP
  - `1`：发送消息，并会等待leader 收到确认后，一定的可靠性
  - `-1`：发送消息，等待leader收到确认，并进行复制操作后，才返回，最高的可靠性
* `request.timeout.ms = 10000` 
  消息发送的最长等待时间
* `send.buffer.bytes=100*1024`
  socket的缓存大小
* `key.serializer.class`
  key的序列化方式，若是没有设置，同`serializer.class`
* `partitioner.class=kafka.producer.DefaultPartitioner`
  分区的策略，默认是取模
* `compression.codec = none`
  消息的压缩模式，默认是none，可以有gzip和snappy
* `compressed.topics=null`
  可以针对默写特定的topic进行压缩
* `message.send.max.retries = 3`
  消息发送失败后的重试次数
* `retry.backoff.ms = 100`
  每次失败后的间隔时间
* `topic.metadata.refresh.interval.ms = 600 * 1000` 
  生产者定时更新topic元信息的时间间隔 ，若是设置为0，那么会在每个消息发送后都去更新数据
* client.id=""
  用户随意指定，但是不能重复，主要用于跟踪记录消息
 
**消息模式 相关**
* `producer.type=sync`
  生产者的类型 async:异步执行消息的发送 sync：同步执行消息的发送
* `queue.buffering.max.ms = 5000`
  异步模式下，那么就会在设置的时间缓存消息，并一次性发送
* `queue.buffering.max.messages = 10000`
  异步的模式下 最长等待的消息数
* `queue.enqueue.timeout.ms = -1`
  异步模式下，进入队列的等待时间 若是设置为0，那么要么进入队列，要么直接抛弃
* `batch.num.messages=200`
  异步模式下，每次发送的最大消息数，前提是触发了`queue.buffering.max.messages`或是`queue.buffering.max.ms`的限制
* `serializer.class = kafka.serializer.DefaultEncoder` 
  消息体的系列化处理类 ，转化为字节流进行传输


#### 示例
`server.properties` (kafka_2.12-0.10.2.1.tar.gz 自带)
```bash
#### Server Basics
broker.id=0

num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600

#### Log Basics
log.dirs=/tmp/kafka-logs
num.partitions=1
## ????
num.recovery.threads.per.data.dir=1  

####  Log Flush Policy
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000

####  zookeeper
zookeeper.connect=localhost:2181
zookeeper.connection.timeout.ms=6000
```

`server.properties` (kafka/templates/server.properties)
```bash
#### Server Basics
broker.id={{ kafka_broker_id1 }}
port={{ kafka_port1 }}

#### Zookeeper
zookeeper.connect={% for host in vars['zookeeper_connect'] %}{{ host }}:2181{% if not loop.last %},{% endif %}{% endfor %}

#### Log Basics
log.dirs={{ vars['kafka_data'] }}

#### ------------------- ####
num.partitions=30
message.max.bytes=1000000
auto.create.topics.enable=false
log.index.interval.bytes=4096
log.index.size.max.bytes=10485760
log.retention.hours=12
log.flush.interval.ms=10000
log.flush.interval.messages=20000
log.flush.scheduler.interval.ms=2000
log.roll.hours=12
log.retention.check.interval.ms=300000
log.segment.bytes=1073741824

# Replication configurations
num.replica.fetchers=4
replica.fetch.max.bytes=1048576
replica.fetch.wait.max.ms=500
replica.high.watermark.checkpoint.interval.ms=5000
replica.socket.timeout.ms=30000
replica.socket.receive.buffer.bytes=65536
replica.lag.time.max.ms=10000
replica.lag.max.messages=4000

controller.socket.timeout.ms=30000
controller.message.queue.size=10

# ZK configuration
zookeeper.connection.timeout.ms=6000
zookeeper.sync.time.ms=2000

# Socket server configuration
num.io.threads=20
num.network.threads=20
socket.request.max.bytes=104857600
socket.receive.buffer.bytes=1048576
socket.send.buffer.bytes=1048576
queued.max.requests=1000
fetch.purgatory.purge.interval.requests=100
producer.purgatory.purge.interval.requests=100
```


## KAFKA Introduction
#### Introduction

concepts
* Kafka is run as a cluster on one or more servers.
* The Kafka cluster stores streams of records in categories called topics.
* Each record consists of a key, a value, and a timestamp.

Kafka has four core APIs:
* The Producer API allows an application to publish a stream of records to one or more Kafka topics.
* The Consumer API allows an application to subscribe to one or more topics and process the stream of records produced to them.
* The Streams API allows an application to act as a stream processor, consuming an input stream from one or more topics and producing an output stream to one or more output topics, effectively transforming the input streams to output streams.
* The Connector API allows building and running reusable producers or consumers that connect Kafka topics to existing applications or data systems.



#### Quick Start

1. Start the server  
  启动 zookeeper: `bin/zookeeper-server-start.sh config/zookeeper.properties`  
  启动 kafka: `bin/kafka-server-start.sh config/server.properties`   
2. Create a topic
  `bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test`
  查看 topic: `bin/kafka-topics.sh --list --zookeeper localhost:2181`
3. Send some messages
  `bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test`
4. Start a consumer
  `bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning`
5. Setting up a multi-broker cluster



#### Apache Storm and Kafka Cluster with Docker
storm-kafka-compose.yml
```yaml
zookeeper:
  image: jplock/zookeeper
  ports:
    - "2181:2181"
nimbus:
  image: wurstmeister/storm-nimbus:0.9.2
  ports:
    - "3773:3773"
    - "3772:3772"
    - "6627:6627"
  links:
    - zookeeper:zk
supervisor:
  image: wurstmeister/storm-supervisor:0.9.2
  ports:
    - "8000:8000"
  links:
    - nimbus:nimbus
    - zookeeper:zk
ui:
  image: wurstmeister/storm-ui:0.9.2
  ports:
    - "8080:8080"
  links:
    - nimbus:nimbus
    - zookeeper:zk
kafka1:
  image: wurstmeister/kafka:0.8.1
  ports:
    - "9092:9092"
  links:
    - zookeeper:zk
  environment:
    BROKER_ID: 1
    HOST_IP: 192.168.59.103
    PORT: 9092
kafka2:
  image: wurstmeister/kafka:0.8.1
  ports:
    - "9093:9093"
  links:
    - zookeeper:zk
  environment:
    BROKER_ID: 2
    HOST_IP: 192.168.59.103
    PORT: 9093
openfire:
  image: mdouglas/openfire
  ports:
  - "5222:5222"
  - "5223:5223"
  - "9091:9091"
  - "9090:9090"
```

Open browser and go to these URL’s to confirm the Cluster is running:
* Supervisor Log ==> http://192.168.59.103:8000/log?file=supervisor.log
* Storm UI ==> http://192.168.59.103:8080/index.html
* Openfire XMPP  ==> http://192.168.59.103:9090/
