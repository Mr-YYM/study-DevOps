# Elasticsearch



## 安装步骤

我的部署环境是 **Centos 7** 。

安装方法有两个，一种是使用官方提供的**压缩包**直接运行，除非是在本地电脑测试，如果是云主机运行的话需要一些额外的配置，过程相对比较复杂。另外一种是利用 **docker** 包，直接运行，可以省去许多步骤，部署起来相对简单。

### 压缩包安装

  ⚠️ ！！！如果直接在 root 用户下运行 elasticsearch 会报错：`can not run elasticsearch as root`

- 如果主机只有 root 用户，需要新建一个用户[2]

  ```shell
  useradd -m [username] # 添加用户
  passwd [username] # 给用户设置密码
  
  # --- optional ----
  userdel -r [username] # 删除一个用户
  ```


  > 1. 在root权限下，useradd只是创建了一个用户名，如（`useradd + 用户名 `），它并没有在`/home`目录下创建同名文件夹，也没有创建密码，因此利用这个用户登录系统，是登录不了的，为了避免这样的情况出现，可以用 （`useradd -m +用户名`）的方式创建，它会在`/home`目录下创建同名文件夹，然后利用（ `passwd + 用户名`）为指定的用户名设置密码。
  >
  > 2. 可以直接利用adduser创建新用户（`adduser +用户名`）这样在/home目录下会自动创建同名文件夹  

- 切换用户

  ```shell
  su [username] # 切换到用户[username]
  
  # --- optional ---
  su root # 切换到 root 用户。一般需要输入密码
  ```

- 下载与解压缩安装包，运行测试[1]

  - 安装包下载

    最新版本下载：https://www.elastic.co/cn/downloads/elasticsearch

    ```shell
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-linux-x86_64.tar.gz # 当然也可以用 wget 下载
    ```

  - 解压缩安装包

    ```sh
    tar -xvf elasticsearch-7.3.0-linux-x86_64.tar.gz
    ```
    
  - 尝试运行
  
    ```shell
    cd elasticsearch-7.3.0/bin
    ./elasticsearch
    
    # 提示了 started 证明启动成功
    [2019-08-01T11:46:51,496][INFO ][o.e.n.Node] [.......] started
    ```
  
  - 测试
  
    ```shell
    curl localhost:9200
    ```
  
    正常运行会有以下输出
  
    ```json
    {
      "name" : "localhost.localdomain",
      "cluster_name" : "elasticsearch",
      "cluster_uuid" : "......",
      "version" : {
        "number" : "7.2.0",
        "build_flavor" : "default",
        "build_type" : "tar",
        "build_hash" : "......",
        "build_date" : "2019-06-20T15:54:18.811730Z",
        "build_snapshot" : false,
        "lucene_version" : "8.0.0",
        "minimum_wire_compatibility_version" : "6.8.0",
        "minimum_index_compatibility_version" : "6.0.0-beta1"
      },
      "tagline" : "You Know, for Search"
    }
    ```



通过以上的这些步骤，基本上就可以本地主机（自己的电脑）运行进行测试运行学习各种操作了。但如果想在云主机下面运行，并开放公网访问就要进行更多的配置。

- 修改配置文件

  ```yaml
  # vim config/elasticsearch.yml
  # 修改这一条
  network.host: 0.0.0.0
  ```

- 重新运行大概会发现三处报错，我们依次解决：

  ```shell
  # max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
  cp /etc/sysctl.conf /etc/stsctl.conf.backup
  sudo echo "vm.max_map_count=2662144" >> /etc/sysctl.conf
  sysctl -p
  sysctl wm.map_map_count # 验证是否生效
  
  # max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
  su root
  ulimit -n 65535
  ## 这条命令在普通用户下无法运行，需要用 su root 切换到 root 更改完后再切回去。这只是个临时性的办法。
  su [not_root_username]
  
  # the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
  
  vim config/elasticsearch.yml
  # 修改以下内容：
  cluster.initial_master_nodes: ["node-1"]
  ```

- 放行 9200 端口

  ```shell
  firewall-cmd --add-port=9200/tcp --permanent
  firewall-cmd --reload
  ```

- 重启elasticsearch

  ```shell
  cd elasticsearch-7.3.0/bin
  ./elasticsearch
  
  # 提示了 started 证明启动成功
  [2019-08-01T11:46:51,496][INFO ][o.e.n.Node] [.......] started
  ```

  


## 参考资料

1. [official: elasticsearch installation](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-install.html)
2. [CSDN: linux创建用户、设置密码](https://blog.csdn.net/li_101357/article/details/69367457)
3. [CSDN: elasticsearch安装 及 启动异常解决](https://blog.csdn.net/happyzxs/article/details/89156068)

