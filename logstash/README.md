Logstash
--------

安装
====




<img alt="logstash" src="http://7xidkg.com1.z0.glb.clouddn.com/logstash-architecture.png" width="572" height="323" />

shipper等价于应用机器上的agent，通过监听`事件`统一规整到Broker(相当于一个buffer)，indexer是就是logstash的server部分。本身上来讲logstash不细分角色，其input-filter-output的机制，灵活度很高。对于Storage部分，Elasticsearch提供了全文索引，最后通过Kibana展现。

一个符合SLA协议的部署结构大致可以是这样的，官方参考。

<img alt="logstash" src="http://7xidkg.com1.z0.glb.clouddn.com/logstash-architecture-final.png" width="572" height="323" />
