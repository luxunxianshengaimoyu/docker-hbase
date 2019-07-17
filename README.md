# docker部署hbase集群

### 1. 安装docker，版本要求1.13.1  
 
### 2. 加载image  
* `docker load < docker-hbase.tar`  

### 3. 创建目录作为hadoop的数据节点
* `mkdir -p /home/hdfs/data/namenode`  
* `mkdir -p /home/hdfs/data/datanode1`  
* `mkdir -p /home/hdfs/data/datanode2`  
* `mkdir -p /home/hdfs/data/datanode3`

### 4. 启动一个容器作为hadoop master节点  
* `docker run -it --name hadoop-master -h hadoop-master -d -P -p 50070:50070 -p 8088:8088 -p 60010:60010 -p 8889:8889 -v /home/hdfs:/home/hdfs haohan/hbase:2.0`

### 5. 启动三个容器作为hadoop slave节点(N=1,2,3)  
* `docker run -it --name hadoop-slaveN -h hadoop-slaveN -d -v /home/hdfs:/home/hdfs haohan/hbase:2.0`  

### 6. 进入hadoop master容器，修改/etc/hosts文件和hadoop配置文件
* `echo 172.17.0.2 hadoop-master >> /etc/hosts`  
* `echo 172.17.0.3 hadoop-slave1 >> /etc/hosts`  
* `echo 172.17.0.4 hadoop-slave2 >> /etc/hosts`  
* `echo 172.17.0.5 hadoop-slave3 >> /etc/hosts`    

### 7. 进入hadoop slave容器，修改zookeeper的myid文件并启动zookeeper服务(N=1,2,3)
* `docker exec -it -u root hadoop-slaveN /bin/bash`  
* `echo N > /usr/local/zookeeper/data/myid`  
* `/usr/local/zookeeper/bin/zkServer.sh start`   

### 8. 进入hadoop-master容器，启动hadoop服务  
* `docker exec -it -u root hadoop-master /bin/bash`
* `hdfs namenode -format`  
* `/usr/local/hadoop/sbin/start-dfs.sh`  
* `/usr/local/hadoop/sbin/start-yarn.sh` 

### 9. 启动hbase服务
* `start-hbase.sh`   
