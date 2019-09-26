# Prometheus

- Prometheus 是使用 Go 语言开发的
- Prometheus 是一种 TSDB( Time series Database 时序列数据库)
- TSDB feature
  - 大部分时间处于写入状态
  - 按顺序写入，按时间顺序排列
  - 进行删除操作是一般删除一整块数据，而不会只删除其中一条数据
  - 没必要做缓存
  - 高并发读操作很常见
- Prometheus 组件
  - Prometheus server —— 数据采集和存储，提供PromQL查询语言的支持。
  - client SDK —— 官方类库 Java Python Go Ruby
  - Push Gateway —— 中间网关for supporting short-lived jobs
  - exporters 
  - alert manager —— 处理告警
  - various support tools
  - 组件架构图：：
  - ![组件架构图](https://www.hi-linux.com/img/linux/prometheus-arch.png)
  - ![](https://prometheus.io/assets/architecture.png)

> https://www.hi-linux.com/posts/25047.html
>
> ####  Prometheus的特点
>
> - 多维度数据模型。
> - 灵活的查询语言。
> - 不依赖分布式存储，单个服务器节点是自主的。
> - 通过基于HTTP的pull方式采集时序数据。
> - 可以通过中间网关进行时序列数据推送。
> - 通过服务发现或者静态配置来发现目标服务对象。
> - 支持多种多样的图表和界面展示，比如Grafana等。