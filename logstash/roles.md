# 几种角色
input -> filter -> output

## 收集器 shipper

部署在`目标机器`上的logstash，实时监控log文件的变化，并将其传递给中间人（建议的做法）。

## 代理人 broker

`broker`在这里，更多的用途是`解耦`。

## 索引器 indexer

indexer将broker中的数据通过`elasticsearch`的output插件持久化到`elasticsearch`集群。