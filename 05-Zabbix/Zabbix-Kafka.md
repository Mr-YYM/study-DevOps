# Zabbix 监控 Kafka

reference：https://www.jianshu.com/p/c56e60d35b35

## 创建 Zookeeper 与 Kafka 容器

```yml
# vim docker-compose.yml
version: '3'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
  kafka:
    image: wurstmeister/kafka
    container_name: kafka-test
    depends_on: [ zookeeper ]
    expose:
      - "10049" # JMX 监听端口，可以自定义设置
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 192.168.5.31
      KAFKA_CREATE_TOPICS: "test:1:1"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

- 运行 命令

  ```shell
  docker-compose up -d
  ```

## 进入容器修改配置

```shell
# docker exec -it kafka-test bash
# vi /opt/kafka_2.12-2.3.0/bin/kafka-run-class.sh
# ⬇️⬇️⬇️找到一下类似的一段内容，替换掉⬇️⬇️⬇️
# JMX settings
if [ -z "$KAFKA_JMX_OPTS" ]; then
  KAFKA_JMX_OPTS="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=10049 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false "
fi
```

- 重启容器

  ```shell
  docker restart kafka-test
  ```

- 使容器与 zabbix-java-gateway 容器在同一个网段内

  ```shell
  docker network connect kafka-test zabbix-JavaGateway --alias zabbix-JavaGateway
  ```

## 配置模板

- 下载模板文件 https://raw.githubusercontent.com/helli0n/kafka-monitoring/master/zbx_kafka_templates.xml

- 导入模板

  <img src="/Users/yanyeming/Desktop/study-DevOps/05-Zabbix/assets/06-template02.png" alt="06-template02" style="zoom: 50%;" />

  

- 给需要监控的主机添加相关模板

  <img src="/Users/yanyeming/Desktop/study-DevOps/05-Zabbix/assets/06-add-template-to-host.png" alt="06-add-template-to-host" style="zoom: 33%;" />

