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
