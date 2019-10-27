# ELK Stack

## Why do we need Log Analysis

- 对一个运维来说，日志对他非常重要。日志能够帮助我们定位分析问题所在【 troubleshooting 】，了解软件的运行情况。
- 软件的管理者需要学会的第一件事情就是要学会看日志与分析日志
- Log can be centralized and decentralized
  - decentralized: log 分散在各个服务器上 we have to log in each server to drill down the issue.
  - central place help us with log analysis. This is what ELK Stack do. 
- **Log Analysis is the Process of analyzing the computer/ machine generated data.**    

![](https://raw.githubusercontent.com/Mr-YYM/study-DevOps/master/01-ELK-Stack/01-ELK-basic/assets/01-what-is-log-analysis.png)

-  log always unstructured form. 

## Need for Log Analysis

- Issue Debugging
  - we come across an issue in the production scenarios(场景) the we go the see the log to debug the log.
  - keeping reading logs in a regular interval, it can reduce the occurrence of a certain issue or certain error which can be **predictive** 【与下面对应】
- Predictive Analysis
  - 通过日志推测可能发生的问题
- Security Analysis
  - Capture attack
- Performance Analysis
  - Why It is slow
- Internet of Things & Dubugging 
  - IOT

 ## Problems with Log analysis

- Non-consistent log format

![](https://raw.githubusercontent.com/Mr-YYM/study-DevOps/master/01-ELK-Stack/01-ELK-basic/assets/02-Non-consistent-log-format.png)

- Non-consistent time format
- Decentralized logs
- Expert knowledge requirement

## What is ELK Stack

ELK Stack is a combination of **three open sorce tools** which forms a log management tool/ platform, that helps in deep **searching, analyzing and visualizing the log** generater from different machines. 

- elasticsearch
  - storing the logs in the JSON format, indexing it and allowing the searching of the logs. 
- logstash
- kibana

## References

1. https://www.youtube.com/watch?v=MRMgd6E9AXE
