# Dockerfile NOTE

## Best Practices

### 多阶段 build 有效控制镜像大小
多阶段构建镜像可以有效降低镜像的大小，并且第一个阶段会默认使用缓存，这样构建出错就不需要担心依赖要重新下载安装了。

#### 构建一个 Django 项目镜像

**普通 Dockerfile：**
```Dockerfile
FROM alpine:3.12
WORKDIR /app

COPY ./Shanghai /etc/localtime
COPY . .

RUN echo "Asia/Shanghai" > /etc/timezone && \
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

RUN apk add python3-dev py3-pip libc-dev mysql-dev gcc && \
    pip3 install setuptools==45 -i https://pypi.douban.com/simple && \
    pip3 install -r requirement.txt -i https://pypi.douban.com/simple

EXPOSE 8000

ENTRYPOINT ["python", "manage.py", "runserver"]
```

**多阶段构建 Dockerfile：**
```Dockerfile
FROM alpine:3.12 as builder
WORKDIR /app

COPY ./requirement.txt .

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

RUN mkdir /install && \
    apk add python3-dev py3-pip libc-dev mysql-dev gcc && \
    pip3 install setuptools==45 -i https://pypi.douban.com/simple && \
    pip3 install -r requirement.txt -i https://pypi.douban.com/simple

FROM python:3.8.3-alpine3.12
WORKDIR /app

COPY . /app
COPY --from=builder /usr/lib/python3.8/site-packages /usr/local/lib/python3.8/site-packages
COPY --from=builder /usr/lib/libmysqlclient* /usr/local/lib/

RUN mv ./Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone && \
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && \
    apk add mysql-dev

EXPOSE 8000

ENTRYPOINT ["python3", "manage.py", "runserver", "0.0.0.0:8000"]
```

**前后镜像大小对比**
```shell
docker image ls|grep i-test
# i-test2          latest              804e5e8461dd        About an hour ago   173MB
# i-test           latest              2d8fcf274c2a        3 hours ago         314MB
```
