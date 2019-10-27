# Docker 基础



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



## 参考资料

1. [Get Docker engine](https://docs.docker.com/install/linux/docker-ce/centos/)