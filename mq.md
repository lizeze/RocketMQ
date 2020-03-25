
# 环境
 * 建议使用Linux 64位操作系统
 * 64位JDK 1.8+
 * Maven 3.2.x
 
# 配置环境

## JAVA

 使用`yum`安装
   
   执行命令
   ```
   $ yum install java-1.8.0-openjdk-devel.x86_64
   ```
 配置全局变量


 ```
 vi /etc/profile
 ```
 复制下面三行到文件中

```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.171-8.b10.el6_9.x86_64
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```
使环境变量生效
```
$ source /etc/profile
```
 验证安装是否成功
  
 ```
 $ java -version

 openjdk version "1.8.0_242"
 OpenJDK Runtime Environment (build 1.8.0_242-b08)
 OpenJDK 64-Bit Server VM (build 25.242-b08, mixed mode)

 ```


 ## maven


创建安装目录
```
 $ mkdir /usr/local/maven
 $ cd /usr/local/maven
```
 下载`maven`安装程序

```
$  wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
```
 解压安装

 ```
 $ tar -zxvf apache-maven-3.3.9-bin.tar.gz
 ```

 修改环境变量
 ```
 $ vim /etc/profile
 ```

在文件末尾增加
```
     M2_HOME==/usr/local/maven/apache-maven-3.3.9
     export PATH=${M2_HOME}/bin:${PATH}
```

使环境变量生效
```
 $ source /etc/profile
```
验证安装是否成功
```
 $ mvn -v
```


>Error: JAVA_HOME is not defined correctly.
 We cannot execute /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.171-8.b10.el6_9.x86_64/bin/java

如果出现以上错误需要修改环境变量
```
$ vim /etc/profile
```

JAVA_HOME修改为
```
export JAVA_HOME=/usr
```
使环境变量生效
```
 $ source /etc/profile
```
 查看`maven`版本
 ```
 $ mvn -v
  Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
  Maven home: /usr/local/maven/apache-maven-3.3.9
  Java version: 1.8.0_242, vendor: Oracle Corporation
  Java home: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64/jre
  Default locale: en_US, platform encoding: UTF-8
  OS name: "linux", version: "3.10.0-1062.4.1.el7.x86_64", arch: "amd64", family: "unix"

 ```



# 服务器
 ## 安装

* [下载](https://archive.apache.org/dist/rocketmq/4.7.0/rocketmq-all-4.7.0-source-release.zip)源代码到服务器
* 执行解压命令并构建代码
```
$ unzip rocketmq-all-4.7.0-source-release.zip
$ cd rocketmq-all-4.7.0/
$ mvn -Prelease-all -DskipTests clean install -U
$ cd distribution/target/rocketmq-4.7.0/rocketmq-4.7.0
```
## 修改配置
 如果是在虚拟机内存较小的环境下执行下面方法，生产环境下最好使用默认的配置。
 
```
 $  vi bin/runbroker.sh

 JAVA_OPT="${JAVA_OPT} -server -Xms128m -Xmx128m -Xmn128m"

```
```
$ vi bin/runserver.sh
JAVA_OPT="${JAVA_OPT} -server -Xms128m -Xmx128m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"

```
访问配置

```
$ vim conf/broker.conf
```
添加
```
namesrvAddr = xx.xx.xx.xx:9876;xx.xx.xx.xx:9876  (多个地址以;分隔)
brokerIP1 = xx.xx.xx.xx
```

启动namesrv

```
$ sh bin/mqnamesrv -n "106.13.16.32:9876"  &
```

启动启动broker
```
$ sh bin/mqbroker -n 106.13.16.32:9876  -c conf/broker.conf  autoCreateTopicEnable=true &

```

# 客户端配置

 增加依赖 

 >客户端版本要和服务器版本一致

 ```
        <dependency>
            <groupId>org.apache.rocketmq</groupId>
            <artifactId>rocketmq-client</artifactId>
            <version>4.7.0</version>
        </dependency>
 ```

## Producer

```


   DefaultMQProducer producer = new DefaultMQProducer("lzz23");
        producer.setNamesrvAddr("http://106.13.16.32:9876");
        try {
            producer.start();

            Message msg = new Message("TopicTest31",
                    "push",
                    "1",
                    "Just for test.".getBytes());

            SendResult result = producer.send(msg);
            System.out.println("id:" + result.getMsgId() +
                    " result:" + result.getSendStatus());

            msg = new Message("TopicTest31",
                    "push",
                    "2",
                    "Just for test.".getBytes());

            result = producer.send(msg);
            System.out.println("id:" + result.getMsgId() +
                    " result:" + result.getSendStatus());

            msg = new Message("TopicTest31",
                    "push",
                    "1",
                    "Just for test.".getBytes());

            result = producer.send(msg);
            System.out.println("id:" + result.getMsgId() +
                    " result:" + result.getSendStatus());
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            producer.shutdown();
        }
```


## Consumer

```
 DefaultMQPushConsumer consumer =
                new DefaultMQPushConsumer("lzz23");
        consumer.setNamesrvAddr("106.13.16.32:9876");
        try {
            //订阅PushTopic下Tag为push的消息
            consumer.subscribe("TopicTest31", "push");

            //程序第一次启动从消息队列头取数据
            consumer.setConsumeFromWhere(
                    ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
            consumer.registerMessageListener(new MessageListenerConcurrently() {
                                                 public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, ConsumeConcurrentlyContext Context) {
                                                     Message msg = list.get(0);
//                            System.out.println(msg.toString());

                                                     String topic = msg.getTopic();
                                                     System.out.println("topic = " + topic);
                                                     byte[] body = msg.getBody();
                                                     System.out.println("body:  " + new String(body));
                                                     String keys = msg.getKeys();
                                                     System.out.println("keys = " + keys);
                                                     String tags = msg.getTags();
                                                     System.out.println("tags = " + tags);
                                                     System.out.println("-----------------------------------------------");

                                                     return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
                                                 }
                                             }
            );
            consumer.start();
        } catch (Exception e) {
            e.printStackTrace();
        }
```

