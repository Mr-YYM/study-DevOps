# Docker 基础

###  一、环境配置的难题

> 软件开发最大的麻烦事之一，就是环境配置。用户计算机的环境都不相同，你怎么知道自家的软件，能在那些机器跑起来？
>
> 用户必须保证两件事：操作系统的设置，各种库和组件的安装。只有它们都正确，软件才能运行。举例来说，安装一个 Python 应用，计算机必须有 Python 引擎，还必须有各种依赖，可能还要配置环境变量。
>
> 如果某些老旧的模块与当前环境不兼容，那就麻烦了。开发者常常会说："它在我的机器可以跑了"（It works on my machine），言下之意就是，其他机器很可能跑不了。
>
> 环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。

## 什么是 docker



## Docker 的安装

- 系统要求：CentOS 7

- 安装 Docker repository

  ```shell
  # 安装必要的包
  sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2
  
  # Use the following command to set up the stable repository.
  sudo yum-config-manager \
      --add-repo \
      https://download.docker.com/linux/centos/docker-ce.repo
  ```

- 安装 Docker CE

  ```shell
  sudo yum install docker-ce docker-ce-cli containerd.io
  ```

- 启动 Docker

  ```shell
  sudo systemctl start docker
  sudo systemctl enable docker
  ```

- 确认 Docker 成功安装，并能够运行

  ```shell
  sudo docker run hello-world
  ```

## Docker 基础命令

### create start run 的区别

> **Create** adds a writeable container on top of your image and sets it up for running whatever command you specified in your `CMD`. The container ID is reported back but it’s not started.
>
> **Start** will start any stopped containers. This includes freshly created containers.
>
> **Run** is a combination of create and start. It creates the container and starts it.

- `create`是创建新的容器
- `start` 启动一个停止的容器包括刚刚`create`的容器
- `run`包括了`create`与`start`

## Docker 常用命令一览

```shell
### 查看 docker 命令列表
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq

docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyhello" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry

docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager


```

## Dockerfile sample

```dockerfile
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```


## 最佳实践

### 修改容器数据的默认存放位置

参考：https://stackoverflow.com/questions/36014554/how-to-change-the-default-location-for-docker-create-volume-command

- 修改 `/lib/systemd/system/docker.service` 文件中 `Service` 项中的 `ExecStart` ，在命令后添加参数 `--data-root=[自定义的目录] `即可

  ```ini
  # vim /lib/systemd/system/docker.service
  [Service]
  ...
  ExecStart=/usr/bin/dockerd --data-root=/docker_data -H fd:// --containerd=/run/containerd/containerd.sock
  ...
  ```

- 重启 docker
- 建议第一次安装就要设置，更改后数据会消失

## 参考资料

1. [Get Docker engine](https://docs.docker.com/install/linux/docker-ce/centos/)
2. [ 阮一峰 Docker 入门](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

