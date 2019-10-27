# Zabbix

- zabbix 是一个基于 WEB 界面的提供分布式系统监视以及网络监视功能的企业级开源解决方案（基于 GPL V2）
  - Web 界面：基于 B/S 结构 ，基于 C/S 结构收集数据。拥有状态展示（状态是否正常，显示红色绿色）与图表展示📈 
  - 分布式系统监控：多个分区服务器监控，降低总服务器压力
  - 企业级开源解决方案：企业级——稳定，高效；开源——免费；GPL V2——可修改源码，可进行售卖但必须提供源码包。
- zabbix 由两部分组成： zabbix server、zabbix agent

## 相关组件

<img src="./assets/01-zabbix-structure.png" alt="01-zabbix-structure" style="zoom: 50%;" />

- zabbix server
  - Zabbix-web-GUI：用户查看与管理的 WEB 界面
  - Zabbix-database：存储收集到的数据，通常为Mysql
  - Zabbix-server：接受来自 agent 或 proxy 的数据
    - IPMI
    - agent
    - ICMP/SNMP：数据收集协议，类似于 TCP 协议。
- zabbix-proxy
  - 分布式采集方案

##  部署 Zabbix Server 端——Docker 大法

### Mysql 的配置安装

- 拉取镜像

  ```shell
  docker pull mysql:5.7.24 # 测试过最新版会报错，只能用旧版了。
  ```

- [tips] 我们需要把 mysql 的数据存储的位置映射出来。如何找到数据存储位置：

  ```mysql
  # 创建一个测试的容器，临时用来查看数据存储的目录。
  # docker run --name test-mysql -t -e MYSQL_ROOT_PASSWORD="xxx" -d mysql:5.7.24
  # docker exec -it test-mysql
  # mysql -u root -p xxx
  mysql> select @@datadir;
  +-----------------+
  | @@datadir       |
  +-----------------+
  | /var/lib/mysql/ |
  +-----------------+
  1 row in set (0.01 sec)
  ```

- 创建一个数据卷

  ```shell
  docker volume create zabbix-mysql
  ```

- 创建并运行一个 Mysql 容器

  ```shell
  # -e 配置容器内环境变量，用来指定相关用户密码数据库名称。
  # --mount 链接刚刚创建的数据卷，用于永久化数据，
  docker run --name zabbix-mysql-server -t \
        -e MYSQL_DATABASE="zabbix" \
        -e MYSQL_USER="zabbix" \
        -e MYSQL_PASSWORD="xxx1" \
        -e MYSQL_ROOT_PASSWORD="xxx2" \
        --mount source=zabbix-mysql,target=/var/lib/mysql \
        -d mysql:5.7.24 \
        --character-set-server=utf8 --collation-server=utf8_bin
  ```

- 启动 Zabbix Java Gateway 实例

  ```shell
  docker run --name zabbix-java-gateway -t \
        -d zabbix/zabbix-java-gateway:latest
  ```

- 启动 Zabbix Server，链接到数据库

  ```shell
  # 注意这里配置的 Mysql 密码要与上面配置的密码相同。
  docker run --name zabbix-server -t \
        -e DB_SERVER_HOST="zabbix-mysql-server" \
        -e MYSQL_DATABASE="zabbix" \
        -e MYSQL_USER="zabbix" \
        -e MYSQL_PASSWORD="xxx1" \
        -e MYSQL_ROOT_PASSWORD="xxx2" \
        -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
        --link zabbix-mysql-server:mysql \
        --link zabbix-java-gateway:zabbix-java-gateway \
        -p 10051:10051 \
        -d zabbix/zabbix-server-mysql:latest
  ```

- 启动 Zabbix Web，链接数据库与 Server

  ```shell
  docker run --name zabbix-web-nginx-mysql -t \
        -e DB_SERVER_HOST="zabbix-mysql-server" \
        -e MYSQL_DATABASE="zabbix" \
        -e MYSQL_USER="zabbix" \
        -e MYSQL_PASSWORD="xxx1" \
        -e MYSQL_ROOT_PASSWORD="xxx2" \
        --link zabbix-mysql-server:mysql \
        --link zabbix-server:zabbix-server \
        -p 80:80 \
        -d zabbix/zabbix-web-nginx-mysql:latest
  ```


## Zabbix agent 部署

### 单机部署

- 添加仓库
  - RHEL 8:

    ```shell
    rpm -Uvh https://repo.zabbix.com/zabbix/4.4/rhel/8/x86_64/zabbix-release-4.4-1.el8.noarch.rpm
    ```

  - RHEL 7:

    ```shell
    rpm -Uvh https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-release-4.4-1.el7.noarch.rpm
    ```

  - RHEL 6:

    ```shell
    rpm -Uvh https://repo.zabbix.com/zabbix/4.4/rhel/6/x86_64/zabbix-release-4.4-1.el6.noarch.rpm
    ```

- 安装与配置被监控主机的`agent`
  - 安装 agent

    ```shell
    yum install -y zabbix-agent
    ```

  - agent 配置 `vim /etc/zabbix/zabbix_agentd.conf`

    ```shell
    PidFile=/var/run/zabbix/zabbix_agentd.pid
    LogFile=/var/log/zabbix/zabbix_agentd.log
    LogFileSize=0
    # 主要修改以下三个，其余默认～
    Server=192.168.5.104 # zabbix server IP 地址
    ListenPort=18888
    ServerActive=192.168.5.104 # zabbix server IP 地址
    Hostname=sunsea3 # 主机名
    Include=/etc/zabbix/zabbix_agentd.d/*.conf
    ```

  - 启动 agent

    ```shell
    systemctl start zabbix-agent.service
    systemctl enable zabbix-agent.service
    ```

  -  开通防火墙 10050/tcp 端口

- server 处在的主机 遭遇 BUG

  ```shell
  Received empty response from Zabbix Agent at [192.168.5.104]. Assuming that agent dropped connection because of access permissions
  ```

  解决办法：暂时无解，无法找到问题所在。**TODO**

### ansible 批量快速部署

- `/etc/ansible/hosts`上配置主机

  ```shell
  # 按照格式配置好资源主机
  # [group_name] 拿来分组
  [k8s-nodes]
  192.168.1.2 hostname=k8s-node1 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass=xxx
  
  192.168.1.3  hostname=k8s-node2 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass=xxx
  
  192.168.1.4  hostname=k8s-node3 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass=xxx
  ```

- 批量添加源

  ```shell
  ansible k8s-nodes -m shell -a "rpm -Uvh https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-release-4.4-1.el7.noarch.rpm" # url参考上方单机部署
  ```

- 批量安装 `agent` 

  ```shell
  ansible k8s-nodes -m yum -a "name=zabbix-agent" # 由于恼人的 GFW ，可能需要重复运行多次才能全部安装成功。
  ```

- 配置 ansible playbook，用于批量修改配置文件

  ```yaml
  # vim ansible-zabbix-agentd.yml
  - hosts: k8s-nodes # 要求与你配置 hosts 时指定的组名一致
    tasks:
      - name: modify server
        lineinfile:
          path: /etc/zabbix/zabbix_agentd.conf
          regexp: '^Server='
          line: Server=192.168.1.xx

      - name: modify port
        lineinfile:
          path: /etc/zabbix/zabbix_agentd.conf
          regexp: '^# ListenPort='
          line: ListenPort=18888

      - name: modify serveractive
        lineinfile:
          path: /etc/zabbix/zabbix_agentd.conf
          regexp: '^ServerActive='
          line: ServerActive=192.168.1.xx

      - name: modify hostname
        lineinfile:
          path: /etc/zabbix/zabbix_agentd.conf
          regexp: '^Hostname='
          line: Hostname={{ hostname }}
  ```

- 运行 `ansible-playbook ansible-zabbix-agentd.yml ` 运行 playbook

- 查看各主机的配置文件修改是否正确

  ```shell
  ansible k8s-nodes -m shell -a 'cat /etc/zabbix/zabbix_agentd.conf |grep -Ev "^$|^#"'
  ```

- 启动 agent 服务

  ```shell
  ansible k8s-nodes -m shell -a "systemctl start zabbix-agent.service"
  ansible k8s-nodes -m shell -a "systemctl enable zabbix-agent.service"
  ```

- 查看端口是否正常监听

  ```shell
  ansible k8s-nodes -m shell -a "netstat -lntp|grep 18888"
  ```

命令行至此部署完毕，可以开始去网页端进行配置了！

## BUGS

```shell
Get value from agent failed: cannot connect to [[192.168.1.xxx]:18888]: [4] Interrupted system call
```

