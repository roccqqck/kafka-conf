# conf

https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/145101/

是否允許自動建立topic ，若是false，就需要通過命令建立topic

```
auto.create.topics.enable =true
```


一個topic ，預設分割槽的replication個數 ，不得大於叢集中broker的個數
```
default.replication.factor =1
```
 

每個topic的分割槽個數，若是在topic建立時候沒有指定的話 會被topic建立時的指定引數覆蓋
```
num.partitions =1
```

例如 --replication-factor3--partitions1--topic replicated-topic ：名稱replicated-topic有一個分割槽，分割槽被複制到三個broker上。


可手動刪除topic
```
delete.topic.enable=true
```


多久自動刪除
```
log.retention.hours=1
```