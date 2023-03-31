#Kafka

  Gonna need zookeeper too

##installation

###zookeeper
sudo yum install kafka zookeeper
y
  sudo vi /etc/zookeeper/zoo.cfg        should start with the number of milliseconds, not making any changes, client port should be 2181
sudo systemctl start zookeeper
^start^status
^status^enable

###kafka
sudo vi /etc/kafka/server.properties     should start with licensed
  :set nu
  line 31 to 'listeners=PLAINTEXT://172.16.50.100:9092'
  line 36 to 'advertised.listeners=PLAINTEXT://172.16.50.100:9092'
  line 60 to 'log.dirs=/data/kafka'
  line 65 change to 3 partitions
  line 107 to 'log.retention.bytes=90000000000'
  line 123 to 'zookeeper.connect=127.0.0.1:2181'
  esc
  :wq
sudo chown -R kafka: /data/kafka
  cd /data        check permissions
  ll

##add ports to firewall
sudo firewall-cmd --add-port=9092/tcp --permanent
sudo firewall-cmd --add-port=2181/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl start kafka
^start^status
^status^enable                    should see both ports to know it is working via "ss-lnt" in ssh session

cd /usr/share/zeek/site/scripts/
sudo curl -LO http://192.168.2.20:8080/zeek_scripts/kafka.zeek      can pull this with other scripts, will need to comment out
ll   to check, should see kafka.zeek
sudo vi kafka.zeek
  on metadata.broker.list set ip to "172.16.50.100:9092"
  esc
  :wq

cd ..                             want to be in /site
sudo vi local.zeek
  shift g
  @load ./scripts/kafka.zeek      add this to the end
sudo systemctl restart zeek
sudo /usr/share/kafka/bin/kafka-topics.sh --bootstrap-server 172.16.50.100:9092 --list
    sudo /usr/share/kafka/bin/kafka-topics.sh --bootstrap-server 172.16.50.100:9092 --describe --topic zeek-raw     alternate option
sudo /usr/share/kafka/bin/kafka-console-consumer.sh --bootstrap-server 172.16.50.100:9092 --topic zeek-raw
    if issues of nothing coming up can ssh in to make traffic
