#Logstash

  Mutate filters


##Install

be ssh in
sudo yum install logstash-7.8.1
y
cd /etc/logstash/
ll
sudo curl -LO http://192.168.2.20:8080/logstash.tar
ll
sudo tar -xf logstash.tar
ll
cd conf.d/
ll
sudo vi logstash-100-input-kafka-zeek.conf
  line 8 change ip to '172.16.50.100:9092'
  :wq

sudo vi logstash-100-input-kafka-suricata.conf
  line 8 change ip to '172.16.50.100:9092'
  :wq

sudo vi logstash-100-input-kafka-fsf.conf
  line 8 ip to '172.16.50.100:9092'
  :wq

sudo vi logstash-9999-output-elasticsearch.conf
  replace all 127.0.0.1 to 172.16.50.100 with below command
  :%s/127.0.0.1/172.16.50.100
    should be 6 sub on 6 line
  # starting line 2
  :wq

cd
sudo systemctl start logstash
^start^status
^status^enable
cat /var/log/logstash/logstash-plain.log

    Fixing issue we made
    cd /etc/elasticsearch
    sudo vi /etc/elasticsearch/elasticsearch.yml
      line to _eno1_
    sudo systemctl stop elasticsearch
    ^stop^start
    ^start^status

    go to kibana index patterns again
    create index pattern
    name : ecs-suricata-*
    next
    @timestamp
    create
      ^repeat above but ecs-zeek-* instead of suricata
      ^repeat above but fsf-* instead of ecs-zeek-*
      Both should show up in index patterns
      dashboard should work now
