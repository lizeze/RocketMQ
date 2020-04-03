# 服务器地址

- 154.8.228.167
- 106.13.16.32
# 常用命令
 * 列出所有使用端口
  ```
  netstat -ntlp
  ```

  * 查看防火墙状态
   
   ```
   firewall-cmd --state
   ```
   * 关闭防火墙
   ```
   systemctl stop firewalld.service
   ```
   * 开启防火墙 
   ```
   systemctl start firewalld.service

   ```
   * 禁止开机启动
   ```
 systemctl disable firewalld.service
   ```

#  创建存储目录
```
  mkdir  -p /usr/local/rocketmq/rocketmq-4.2/data/store
  mkdir -p /usr/local/rocketmq/rocketmq-4.2/data/store/commitlog
   mkdir -p /usr/local/rocketmq/rocketmq-4.2/data/store/consumequeue
    mkdir -p /usr/local/rocketmq/rocketmq-4.2/data/store/index
 
```
