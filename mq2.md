# 集群部署

## 部署结构
 * `Name Server`可集群部署，节点之间无任何信息同步
* `Broker` 是消息中转角色,复制存储消息，转发消息，部署相对复杂
   
   `Broker`分为`Master`和`Slave`,一个`Master`可对应多个`Slave`，但是一个`Slave`只能
   
   对应一个`Master`,`Master`于`Slave`的对应关系通过指定相同的`BrokerName`,不同的
   
   `BrokerId`来定义，`BrokerId`为*0*表示`Master`，非*0*表示`Slave`。`Master`也可以部署多个
  
* `producer`与`name server`集群中的一个节点(随机选择)建立长连接，定期从

  `rameserver`  获取`topic`路由信息，并向提供`fopic`服务的`master`建立长连接，定期向

  `master`发送心跳。`producer`完全无状态，可集群部署。
* `consumer`与`name server`集群中的一个节点（随机选择）建立长连接，

  定期从`name server`获取`topic`路由信息，并向提供服务的`master`、`slave`建立长连
  
  接，定时向`master`、`slave`发送心跳。`consumer`既可以从`master` 订阅消息，也可以从
  
  `Slave` 订阅消息，订阅规则由`Broker` 配置决定。   
   