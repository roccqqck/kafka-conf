# kafka-leader-election.sh

隨著系統的運⾏，broker的宕機重啟，會引發Leader分割區和Follower分割區的⻆⾊轉換，最後可能Leader⼤部分都集中在少數臺broker上，由於Leader負責使用者端的讀寫操作，此時集中Leader分割區的少數臺伺服器的I/O，CPU，以及記憶體都會很緊張。


leader都在broker 1
```
kafka-topics.sh --bootstrap-server kafka:9092 --topic tp_demo_03
```
```
Topic:tp_demo_03 PartitionCount:3 ReplicationFactor:2 Configs:
Topic: tp_demo_03 Partition: 0 Leader: 1 Replicas: 0,1 Isr: 1,0
Topic: tp_demo_03 Partition: 1 Leader: 1 Replicas: 1,0 Isr: 1,0
Topic: tp_demo_03 Partition: 2 Leader: 1 Replicas: 0,1 Isr: 1,0
```

## 建立leader-election.json，內容如下：

指定你要重新分配的topic跟partition
```
{
    "partitions": [
        {
            "topic":"tp_demo_03",
            "partition":0
        },
        {
            "topic":"tp_demo_03",
            "partition":1
        },
        {
            "topic":"tp_demo_03",
            "partition":2
        }
    ]
}
```


## 執⾏操作：
```
kafka-preferred-replica-election.sh --bootstrap-server kafka:9092 --election-type preferred --path-to-json-file leader-election.json
```
```
Created preferred replica election path with {"version":1,"partitions":[{"topic":"tp_demo_03","partition":0},{"topic":"tp_demo_03","partition":1},{"topic":"tp_demo_03","partition":2}]}
Successfully started preferred replica election for partitions Set(tp_demo_03-0, tp_demo_03-1, tp_demo_03-2)
```


## 檢視操作的結果
```
kafka-topics.sh --bootstrap-server kafka:9092 --topic tp_demo_03
```
```
Topic:tp_demo_03 PartitionCount:3 ReplicationFactor:2 Configs:
Topic: tp_demo_03 Partition: 0 Leader: 0 Replicas: 0,1 Isr: 1,0 
Topic: tp_demo_03 Partition: 1 Leader: 1 Replicas: 1,0 Isr: 1,0
Topic: tp_demo_03 Partition: 2 Leader: 0 Replicas: 0,1 Isr: 1,0
```



## 指令選擇topic partition
```
kafka-preferred-replica-election.sh --bootstrap-server kafka:9092 --election-type preferred --all-topic-partitions
```

```
kafka-preferred-replica-election.sh --bootstrap-server kafka:9092 --election-type preferred --topic tp_demo_03 --partition 0
```