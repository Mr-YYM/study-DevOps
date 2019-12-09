# ELK 部署步骤



## Kafka

- single broker 测试

  ```shell
  # 解压
  > tar -xzf kafka_2.12-2.3.0.tgz
  > cd kafka_2.12-2.3.0

  # 启动 zookeeper
  > bin/zookeeper-server-start.sh config/zookeeper.properties
  # 启动 kafka 
  > bin/kafka-server-start.sh config/server.properties

  # 创建一个 Topic
  > bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test

  # list topic
  bin/kafka-topics.sh --list --bootstrap-server localhost:9092

  # 给 Topic 发送消息
  > bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

  # 读取 Topic 的消息
  > bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
  ```

- Multi-broker cluster

  ```yaml
  > cp config/server.properties config/server-1.properties
  > cp config/server.properties config/server-2.properties
  
  config/server-1.properties:
      broker.id=1
      listeners=PLAINTEXT://:9093
      log.dirs=/tmp/kafka-logs-1
   
  config/server-2.properties:
      broker.id=2
      listeners=PLAINTEXT://:9094
      log.dirs=/tmp/kafka-logs-2
      
  > bin/kafka-server-start.sh config/server-1.properties &
  ...
  > bin/kafka-server-start.sh config/server-2.properties &
  ...
  ```

## filebeat

```yaml
output.kafka:
  hosts: ["localhost:9092"]
  topic: test
  required_acks: 1
  
filebeat module enable nginx
filebear -e setup
# 启动
filebeat -e
```



## 参考

http://kafka.apache.org/documentation/#quickstart