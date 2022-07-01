# kafka sh command

[https://kafka.apache.org/0101/documentation.html#linuxflush](https://kafka.apache.org/0101/documentation.html#linuxflush)

[https://microstrategy.com/s/article/How-to-enable-debugging-level-logs-for-Kafka-in-MicroStrategy-2021?language=en_US](https://microstrategy.com/s/article/How-to-enable-debugging-level-logs-for-Kafka-in-MicroStrategy-2021?language=en_US)

# ****常用配置及說明****

[https://www.gushiciku.cn/pl/pMpZ/zh-tw](https://www.gushiciku.cn/pl/pMpZ/zh-tw)

kafka 常見重要配置說明，分為四部分

- Broker Config：kafka 服務端的配置
- Producer Config：生產端的配置
- Consumer Config：消費端的配置
- Kafka Connect Config：kafka 連線相關的配置



# 常用指令

[https://www.796t.com/article.php?id=481462](https://www.796t.com/article.php?id=481462)

[https://cutejaneii.wordpress.com/2017/07/14/kafka-1-常用指令/](https://cutejaneii.wordpress.com/2017/07/14/kafka-1-%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4/)

[https://www.cnblogs.com/wade-luffy/p/6547024.html](https://www.cnblogs.com/wade-luffy/p/6547024.html)

[https://www.gushiciku.cn/pl/pMpZ/zh-tw](https://www.gushiciku.cn/pl/pMpZ/zh-tw)

## **啟動 broker**

`bin/kafka-server-start.sh` 指令碼提供了啟動 broker 的功能.

### **前臺啟動**

```
bin/kafka-server-start.sh config/server.properties

```

### **後臺啟動：**

```
bin/kafka-server-start.sh -daemon config/server.properties

```

## **停止 broker**

`bin/kafka-server-stop.sh` 指令碼提供了停止 broker 的功能.

```
bin/kafka-server-stop.sh
```

## topic相關

### **檢視topic列表**

`bin/kafka-topic.sh`指令碼提供了檢視 topic 列表的功能. 通過 `--list` 引數即可檢視當前叢集所有的 topic 列表.

```
bin/kafka-topics.sh --zookeeper localhost:2181 --list
__consumer_offsets
test
test_topic2
```

### **檢視某個topic的詳細資訊**

`bin/kafka-topic.sh` 指令碼提供了檢視某個 topic 詳細資訊的功能. 通過 `--describe` 引數和 `--topic` 引數指定 topic 名稱即可.

```
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic test
Topic:test PartitionCount:3 ReplicationFactor:2 Configs:
Topic: test Partition: 0 Leader: 2 Replicas: 2,0 Isr: 2,0
Topic: test Partition: 1 Leader: 0 Replicas: 0,1 Isr: 0,1
Topic: test Partition: 2 Leader: 1 Replicas: 1,2 Isr: 1,2
```

### **刪除topic**

`bin/kafka-topic.sh`指令碼提供了刪除 topic 的功能. 通過`--delete` 引數和`--topic`引數指定 topic 名稱即可.

將名稱為 `test` 的 topic 刪除:

```
bin/kafka-topics.sh --zookeeper localhost:2181/kafka --delete --topic test
Topic test is marked for deletion.
Note: This will have no impact if delete.topic.enable is not set to true.

```

注意, broker 配置中必須設定 `delete.topic.enable=true`, 否則刪除操作不會生效.



### **修改topic partition**
partition 數量 只能增加不能減少，就算是指定數量與當下數量一致也會出現錯誤訊息
```
kafka-topics.sh --alter --zookeeper {kafka 位置} --topic {topic 名稱} --partitions {partition 個數}
```


## consumer相關

[https://blog.csdn.net/u010634066/article/details/119670405](https://blog.csdn.net/u010634066/article/details/119670405)

[https://newaurora.pixnet.net/blog/post/227020922-kafka-常用指令](https://newaurora.pixnet.net/blog/post/227020922-kafka-%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4)

### **查看消费者列表`-list`**

```jsx
sh bin/kafka-consumer-groups.sh --bootstrap-server xxxx:9092 --list
```

**查看所有消费组详情`--all-groups`**

```
sh bin/kafka-consumer-groups.sh --bootstrap-server xxxxx:9092 --describe --all-groups
```

**查看指定消费组详情`--group`**

```
sh bin/kafka-consumer-groups.sh --bootstrap-server xxxxx:9092 --describe --group test2_consumer_group
```

**查询消费者成员信息--members**

```jsx
所有消费组成员信息
sh bin/kafka-consumer-groups.sh --describe --all-groups --members --bootstrap-server xxx:9092
指定消费组成员信息
sh bin/kafka-consumer-groups.sh --describe --members --group test2_consumer_group --bootstrap-server xxxx:9092

```

查询消费者状态信息--state

```jsx
所有消费组状态信息
sh bin/kafka-consumer-groups.sh --describe --all-groups --state --bootstrap-server xxxx:9092
指定消费组状态信息
sh bin/kafka-consumer-groups.sh --describe --state --group test2_consumer_group --bootstrap-server xxxxx:9092

```

## **命令列生產者**

`bin/kafka-console-producer.sh` 指令碼能夠通過命令列輸入向指定的 topic 中傳送資料.

向名稱為 test 的 topic 中傳送 3 條資料:

```
bin/kafka-console-producer.sh --broker-list hostname:9092 --topic test
This is a message
This is another message
hello kafka

```

## **命令列消費者**

`bin/kafka-console-consumer.sh`指令碼能夠消費 topic 中的資料並列印到控制檯.

消費名稱為 `test` 的 topic 中的資料:

```
bin/kafka-console-consumer.sh --bootstrap-server hostname:9092 --topic test --from-beginning
This is a message
This is another message
hello kafka
^CProcessed a total of 3 messages

bin/kafka-console-consumer.sh --bootstrap-server hostname:9092 --topic test --from-beginning --group ${consumer groupID}

```

注意, `–from-beginning` 引數表示從 topic 的最開始位置進行消費, 如果沒有指定該引數, 表示從末尾開始消費, 只有當啟動消費者後, 有新的資料寫入, 才能夠顯示到控制檯.

## 常見錯誤處理

[https://support.huaweicloud.com/trouble-mrs/mrs_03_0063.html](https://support.huaweicloud.com/trouble-mrs/mrs_03_0063.html)

[https://www.cnblogs.com/JetpropelledSnake/p/14179662.html](https://www.cnblogs.com/JetpropelledSnake/p/14179662.html)

[https://cloud.tencent.com/developer/article/1508919](https://cloud.tencent.com/developer/article/1508919)

```jsx
have just tried creating a Kafka topic "user:created" and saw this error in Kafka logs: Invalid character ':' in value part of property. I googled and found that in a mailing list people are talking about deprecating . and _ symbols too.
```

因此，最大長度為 255 個符號和字母，`.`（點）、`_`（下劃線）、`-`（減號）可以使用

在 Kafka 0.10 中，maxNameLength 從 255 更改為 249。請參閱[提交](https://github.com/apache/kafka/commit/ad3dfc6ab25c3f80d2425e24e72ae732b850dc60)

此外，帶有句點`.`或下劃線的主題`_`可能會在內部數據結構中發生衝突，因此建議您使用其中一個但*不要同時使用兩者*（[source](https://github.com/apache/kafka/blob/2.3.0/core/src/main/scala/kafka/admin/TopicCommand.scala#L147-L148)）。

[https://stackoverflow.com/questions/71690742/error-disk-error-while-locking-directory-var-kafka-logs-in-3-10-kafka](https://stackoverflow.com/questions/71690742/error-disk-error-while-locking-directory-var-kafka-logs-in-3-10-kafka)

****jstack查看当前进程的状态****

[https://www.cnblogs.com/wade-luffy/p/6547024.html](https://www.cnblogs.com/wade-luffy/p/6547024.html)

```jsx
jstack -l ${pid}
```