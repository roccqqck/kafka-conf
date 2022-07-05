# kafka-reassign-partitions.sh

如果今天有需求將某幾個 topic 全部搬移到指定的某幾個 broker，kafka 有提供腳本 kafka-reassign-partitions.sh 可以批次搬移



## 1. 在開始前先確認目前 topic 的狀態
```
kafka-topics --describe --zookeeper 127.0.0.1:2181 --topic topicWithThreeBroker
```

## 2. 首先需要新增一個 json 檔，填入想要重新分配 partition 的幾個topic
```
vim topics-to-move.json
```
指定一個topic
```
{
  "topics": [
    {"topic": "topicWithThreeBroker"}
  ],
  "version": 1
}
```
指定多個topic
```
{
  "topics": [
    {"topic": "topicWithThreeBroker"},
    {"topic": "anotherTopic"}
  ],
  "version": 1
}
```

## 3. 產生 partition 分配表
參數說明：

```topics-to-move-json-file``` => 填入我們剛剛產生的 json 檔案名稱

```broker-list``` => 可以指定要分配到哪幾個 broker

```generate``` => 會顯示當前 topic 的 partition 分配，並且自動產生我們申請要重新分配 partition 的 topic 的 partition 分配表

將topic partition 分配到broker 0,1
```
kafka-reassign-partitions.sh --bootstrap-server localhost:9092 --topics-to-move-json-file topics-to-move.json --broker-list "0,1," --generate
```
```
Current partition replica assignment
{"version":1,"partitions":[{"topic":"topicWithThreeBroker","partition":0,"replicas":[1,0],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":1,"replicas":[2,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":2,"replicas":[0,2],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":3,"replicas":[1,2],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":4,"replicas":[2,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":5,"replicas":[0,2],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":6,"replicas":[1,2],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":7,"replicas":[2,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":8,"replicas":[0,2],"log_dirs":["any","any"]}]}

Proposed partition reassignment configuration
{"version":1,"partitions":[{"topic":"topicWithThreeBroker","partition":0,"replicas":[1,0],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":1,"replicas":[0,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":2,"replicas":[1,0],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":3,"replicas":[0,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":4,"replicas":[1,0],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":5,"replicas":[0,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":6,"replicas":[1,0],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":7,"replicas":[0,1],"log_dirs":["any","any"]},{"topic":"topicWithThreeBroker","partition":8,"replicas":[1,0],"log_dirs":["any","any"]}]}
```

參數說明：
```topic``` => 指定的 topic

```partition``` => 指定的 partition

```replicas``` => 指定 partition 分配到哪些 broker

```log_dirs``` => 指定 broker 的 log 路徑，預設的 any 代表 broker 可以自由地去選擇 replica 要放在哪裡，目前 broker 實作選擇 log 路徑的方法是 ```round-robin alogorithn```

將 ```current partition``` 的部分備份起來 ```partition_replica_assignment_backup.json```，以防需要rollback

將 ```Proposed partition``` 的部分存到 ```expand_cluster_reassignment.json```


## 4. 用指令verify查看此次將會有什麼變動
```
kafka-reassign-partitions.sh --bootstrap-server localhost:9092 --reassignment-json-file expand_cluster_reassignment.json --verify
```



## 5. 執行重新分配 replica
```
kafka-reassign-partitions.sh --bootstrap-server localhost:9092 --reassignment-json-file expand_cluster_reassignment.json --execute
```


## 6. 最後看一下 topic 是否有重新分配：
```
kafka-topics --describe --zookeeper 127.0.0.1:2181 --topic topicWithThreeBroker
```



### 7. 還原原本分割區
```
kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file partition_replica_assignment_backup.json --verify
kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file partition_replica_assignment_backup.json --execute
```