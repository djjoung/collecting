# Flume Scripts

Basic Flume agent configuration

```properties
# list the sources, sinks and channels for the agent
<Agent>.sources = <Source>
<Agent>.sinks = <Sink>
<Agent>.channels = <Channel1> <Channel2>

# set channel for source
<Agent>.sources.<Source>.channels = <Channel1> <Channel2> ...

# set channel for sink
<Agent>.sinks.<Sink>.channel = <Channel1>

# properties for sources
<Agent>.sources.<Source>.<someProperty> = <someValue>

# properties for channels
<Agent>.channel.<Channel>.<someProperty> = <someValue>

# properties for sinks
<Agent>.sources.<Sink>.<someProperty> = <someValue>
```

## Sources

### Sequence Generator Source

``` properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = seq
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

```shell
$ ./bin/flume-ng agent -c ./conf -f ./conf/seq.conf -name a1
$ ls logs
$ tail -100f logs/flume.log
```

```shell
$ ./bin/flume-ng agent -c ./conf -f ./conf/seq.conf -name a1 \
-Dflume.root.logger=INFO,console
```


### Exec Source

``` properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -f /root/var/data.log
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

```shell
$ ./bin/flume-ng agent -c ./conf -f ./conf/exec.conf -name a1 -Dflume.root.logger=INFO,console
```

```shell
$ echo hello >> /root/var/data/log
$ echo flume >> /root/var/data/log
```

### Syslog TCP Source

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory 
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

```shell
$ ./bin/flume-ng agent -c ./conf -f ./conf/syslogtcp.conf -name a1 -Dflume.root.logger=INFO,console
```

```
$ vi /etc/rsyslog.conf
```

```properties
# remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
#*.* @@remote-host:514
*.* @@localhost:5140
# ### end of the forwarding rule ###
```

```shell
$ logger hello flume
```

### Spooling Directory Source


```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/var/flumespool
a1.sources.r1.fileHeader = true
a1.sources.r1.channels = c1

a1.channels.c1.type = memory 
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

```shell
$ mkdir -p /root/var/flumespool
$ echo hello >> /root/var/flumespool/data1.txt

```

### Avro Source

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = avro
a1.sources.r1.channels = c1
a1.sources.r1.bind = c1
a1.sources.r1.port = 4141

a1.channels.c1.type = memory 
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

## Sink

### HDFS Sink

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = hdfs
a1.sinks.s1.channel = c1
a1.sinks.s1.hdfs.path = /flume/events/%y-%m-%d/%H%M/
a1.sinks.s1.hdfs.filePrefix = events-
a1.sinks.s1.hdfs.fileType = DataStream
a1.sinks.s1.hdfs.round = true
a1.sinks.s1.hdfs.roundValue = 10
a1.sinks.s1.hdfs.roundUnit = minute
```

### Logger Sink

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.channels = c1
a1.sinks = s1
a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Avro Sink

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = avro
a1.sinks.s1.channel = c1
a1.sinks.s1.hostname = localhost
a1.sinks.s1.port = 4545
```

### File Roll Sink

```properties

a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = file_roll
a1.sinks.s1.channel = c1
a1.sinks.s1.sink.directory = /root/var/flume/fileroll
```

```shell
mkdir -p /root/var/flume/fileroll
```


### Kafka Sink

```shell
$ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --topic mytopic --zookeeper localhost:2181
```

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.s1.topic = mytopic
a1.sinks.s1.brokerList = sandbox.hortonworks.com:6667
a1.sinks.s1.requiredAcks = 1
a1.sinks.s1.batchSize = 20
a1.sinks.s1.channel = c1

```

## Channel

### Memory Channel

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.s1.topic = mytopic
a1.sinks.s1.brokerList = sandbox.hortonworks.com:6667
a1.sinks.s1.requiredAcks = 1
a1.sinks.s1.batchSize = 20
a1.sinks.s1.channel = c1
```

### File Channel

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels = c1
a1.channels.c1.type = file
a1.channels.c1.checkpointDir = /root/var/flume/checkpoint
a1.channels.c1.dataDirs = /root/var/flume/data

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Spillable Memory Channel

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels = c1
a1.channels.c1.type = SPILLABLEMEMORY
a1.channels.c1.memoryCapacity = 10000
a1.channels.c1.overflowCapacity = 1000000
a1.channels.c1.byteCapacity = 800000
a1.channels.c1.checkpointDir = /root/var/flume/checkpoint
a1.channels.c1.dataDirs = /root/var/flume/data

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Kafka Channel

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.channels.c1.type   = org.apache.flume.channel.kafka.KafkaChannel
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 1000
a1.channels.c1.brokerList=sandbox.hortonworks.com:6667
a1.channels.c1.topic=flumechannel
a1.channels.c1.zookeeperConnect=localhost:2181

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

## Channel Selectors

### Replicating

```properties
a1.sources = r1
a1.channels = c1 c2 c3
a1.sinks = s1 s2 s3

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost

a1.sources.r1.selector.type = replicating
a1.sources.r1.channels = c1 c2 c3
a1.sources.r1.selector.optional = c3

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.channels.c2.type = memory
a1.channels.c2.capacity = 1000
a1.channels.c2.transactionCapacity = 100

a1.channels.c3.type = memory
a1.channels.c3.capacity = 1000
a1.channels.c3.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1

a1.sinks.s2.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.s2.topic = mytopic
a1.sinks.s2.brokerList = sandbox.hortonworks.com:6667
a1.sinks.s2.requiredAcks = 1
a1.sinks.s2.batchSize = 20
a1.sinks.s2.channel = c2

a1.sinks.s3.type = file_roll
a1.sinks.s3.sink.directory = /root/var/flume/fileroll
a1.sinks.s3.channel = c3
```

### Multiplexing

```properties
a1.sources = r1
a1.channels = c1 c2 c3 c4
a1.sinks = s1 s2 s3 s4

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1 c2 c3 c4

a1.sources.r1.selector.type = multiplexing
a1.sources.r1.selector.header = GENDER
a1.sources.r1.selector.mapping.M = c1
a1.sources.r1.selector.mapping.F = c2 c3
a1.sources.r1.selector.default = c4

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.channels.c2.type = memory
a1.channels.c2.capacity = 1000
a1.channels.c2.transactionCapacity = 100

a1.channels.c3.type = memory
a1.channels.c3.capacity = 1000
a1.channels.c3.transactionCapacity = 100

a1.channels.c4.type = memory
a1.channels.c4.capacity = 1000
a1.channels.c4.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1

a1.sinks.s2.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.s2.topic = mytopic
a1.sinks.s2.brokerList = sandbox.hortonworks.com:6667
a1.sinks.s2.requiredAcks = 1
a1.sinks.s2.batchSize = 20
a1.sinks.s2.channel = c2

a1.sinks.s3.type = file_roll
a1.sinks.s3.sink.directory = /root/var/flume/fileroll
a1.sinks.s3.channel = c3

a1.sinks.s4.type = logger
a1.sinks.s4.channel = c4
```

## Sink Processors

### Failover Sink Processor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1 s2

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.sinkgroups = g1
a1.sinkgroups.g1.sinks = s1 s2
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.s1 = 10
a1.sinkgroups.g1.processor.priority.s2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1

a1.sinks.s2.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.s2.topic = mytopic
a1.sinks.s2.brokerList = sandbox.hortonworks.com:6667
a1.sinks.s2.requiredAcks = 1
a1.sinks.s2.batchSize = 20
a1.sinks.s2.channel = c1
```

### Load balancing Sink Processor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1 s2

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.sinkgroups = g1
a1.sinkgroups.g1.sinks = s1 s2
a1.sinkgroups.g1.processor.type = load_balance
a1.sinkgroups.g1.processor.backoff = true
a1.sinkgroups.g1.processor.selector = round_robin

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1

a1.sinks.s2.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.s2.topic = mytopic
a1.sinks.s2.brokerList = sandbox.hortonworks.com:6667
a1.sinks.s2.requiredAcks = 1
a1.sinks.s2.batchSize = 20
a1.sinks.s2.channel = c1
```

## Interceptors

### Timestamp Interceptor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -f /root/var/data.log
a1.sources.r1.channels = c1

a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = timestamp

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Host Interceptor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -f /root/var/data.log
a1.sources.r1.channels = c1

a1.sources.r1.interceptors = i1 i2
a1.sources.r1.interceptors.i1.type = timestamp

a1.sources.r1.interceptors.i2.type = host
a1.sources.r1.interceptors.i2.hostHeader = hostname

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Static Interceptor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -f /root/var/data.log
a1.sources.r1.channels = c1

a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = static
a1.sources.r1.interceptors.i1.key = GENDER
a1.sources.r1.interceptors.i1.value = M

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### UUID Interceptor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = org.apache.flume.sink.solr.morphline.UUIDInterceptor$Builder
a1.sources.r1.interceptors.i1.headerName = uuid
a1.sources.r1.interceptors.i1.prefix = C

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Morphline Interceptor

### Search and Replace Interceptor

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = s1

a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = search_replace
a1.sources.r1.interceptors.i1.searchPattern = apple
a1.sources.r1.interceptors.i1.replaceString = banana

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sinks.s1.type = logger
a1.sinks.s1.channel = c1
```

### Regex Filtering Interceptor

### Regex Extractor Interceptor
