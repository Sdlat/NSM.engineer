Elasticsearch

  Node: a single instance of Elasticsearch (could be a machine, vm, container, etc)
  Cluster: one or more nodes
  roles: purpose of node server

  Document: a basic unit of info that can be indexed
  Index: logical namespace that refers to a collection of documents
  Shard: an individual piece of an index            < essentially a lucene index



##install

ssh admin@172.16.50.100
sudo yum install elasticsearch-7.8.1
y

    How to uninstall if you install the wrong version
    sudo yum remove elasticsearch
    y

sudo chown elasticsearch: /data/elasticsearch
  cd /data/
  ll


sudo vi /etc/elasticsearch/elasticsearch.yml
  :set nu
  line 17 to 'cluster.name: sg50-cluster'
  line 23 ro 'node.name: sg50-node'
  line 33 to 'path.data: /data/elasticsearch'
  line 43 remove the #
  line 55 'network.host: _eno1_'
  line 59 remove #
  line 68 to 'discovery.type: single-node'
  esc
  :wq

sudo mkdir -p /usr/lib/systemd/system/elasticsearch.service.d/
sudo vi /usr/lib/systemd/system/elasticsearch.service.d/override.conf      making from scratch
```
[Service]
LimitMEMLOCK=infinity
'''
sudo chmod 755 /usr/lib/systemd/system/elasticsearch.service.d
sudo chmod 644 /usr/lib/systemd/system/elasticsearch.service.d/override.conf
sudo systemctl daemon-reload
sudo systemctl start elasticsearch
^start^status
    vi /usr/lib/systemd/system/elasticsearch.service    supports putting a .d in an override.conf file
                              sudo vi /etc/elasticsearch/elasticsearch.yml
                      fixed > could add to line 68 'discovery.type: single-node', would have to recomment 71, and delete 68(discovery,cluster)
sudo systemctl enable elasticsearch

sudo vi /etc/elasticsearch/jvm.options
  :set nu
  line 22 and 23 change the '1's to '4's (-Xms4g, -Xmx4g)
  esc
  :wq
sudo systemctl restart elasticsearch
^restart^status
    journalctl -xeu elasticsearch     to see if we made change or troubleshoot
ss -lnt             should see 9200 and 9300 binded
sudo firewall-cmd --add-port={9200,9300}/tcp --permanent               If firewall is down on test day we auto fail
sudo firewall-cmd --reload
curl localhost:9200
curl localhost:9200/_cat/nodes          <need to make the changes for 68 and 71 in yml to work
