#### On 4 machines build Clickhouse cluster, in cluster create local table replicated in every shard and create distributed tables which will look at two shards and replicas in them.

##### 1. Build Clickhouse cluster
- The first three nodes must have a `zookeeper` in the cluster
- Install Clickhouse on all 4 nodes
- Set the first two nodes as the first shard and two replicas
- Set the second two nodes as second shard and two replicas

##### 1.1 Install Clickhouse on all nodes
```
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates dirmngr
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 8919F6BD2B48D75
echo "deb https://packages.clickhouse.com/deb stable main" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update
sudo apt-get install -y clickhouse-server clickhouse-client
sudo chmod 777 /etc/clickhouse-server/ && sudo chmod 777 /etc/clickhouse-server/config.xml
sudo systemctl start clickhouse-server
```
##### 1.2 Zookeeper config setup

`sudo mv conf/zoo_sample.cfg conf/zoo.cfg`

Setup [config](https://github.com/vadim-davydchenko/Clickhouse_final/blob/master/zoo.cfg) in conf/zoo.cfg

*Test run Zookeeper*

```
sudo ./bin/zkServer.sh start
sudo ./bin/zkCli.sh -server localhost:2181
sudo ./bin/zkServer.sh stop
```
Setup systemd file in [/lib/systemd/system/zookeeper.service](https://github.com/vadim-davydchenko/Clickhouse_final/blob/master/zookeeper.service)

```
sudo useradd -M zookeeper
sudo chown -R zookeeper:zookeeper /opt/zookeeper
sudo chown -R zookeeper:zookeeper /var/lib/zookeeper/
sudo systemctl start zookeeper
```
*Change /etc/hosts, for example*

```
142.93.129.118 server1.694098d80a79581f7c9f432b4fe42541 server1
104.248.92.140 server2.694098d80a79581f7c9f432b4fe42541 server2
142.93.129.38 server3.694098d80a79581f7c9f432b4fe42541 server3
104.248.92.142  server4.694098d80a79581f7c9f432b4fe42541 server4
```
##### 1.3 Configuration `config.xml`for cluster, zookeeper and macros

`vim /etc/clickhouse-server/config.xml`

```
<remote_servers incl="clickhouse_cluster">
</remote_servers>

<include_from>/etc/clickhouse-server/cluster.xml</include_from>

<listen_host>0.0.0.0</listen_host>

<zookeeper incl="zookeeper">
</zookeeper>

<macros incl="macros">
</macros>
```
##### 1.4 Configuration [cluster.xml](https://github.com/vadim-davydchenko/Clickhouse_final/blob/master/cluster.xml) with zookeeper and macros(on the remaining nodes, change the replica values to ch2, ch3, ch4 in the macros)

