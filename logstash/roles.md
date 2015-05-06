# 几种角色
input -> filter -> output

一个符合SLA协议的部署结构大致可以是这样的，官方参考。

<img alt="logstash" src="http://7xidkg.com1.z0.glb.clouddn.com/logstash-architecture-final.png" width="572" height="323" />

## 收集器 shipper

部署在`目标机器`上的logstash，实时监控log文件的变化，并将其传递给中间人（建议的做法）。

## 代理人 broker

`broker`在这里，更多的用途是`解耦`。

## 索引器 indexer

indexer将broker中的数据通过`elasticsearch`的output插件持久化到`elasticsearch`集群。