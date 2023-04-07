##TroubleShooting

physical:

tap monitor ethernet to nook left ethernet
tap 1 ethernet to nook right ethernet
tap 2 ethernet to lan 2 sensor 
sensor lan 1 to them 
tap power to nook hdmi


Document solution and issue

suricata +fsf send data to filebeats
zeek sends to fsf and kafka
kafka to log to elastic to kibana

##Sensor flow
network > inline tap > sensor (port enp5s0) > stenographer (port 1234) (config file in /etc/stenographer/config) (writes to /data)
                      sensor > suricata (/etc/suricata/suricata.yaml)
                      sensor > zeek (zeek-plugin (af-packet)) (output conn.log, extracted_files) (config /etc/zeek/networks.cfg, /etc/zeek/zeekctl.cfg, /etc/zeek/node.cfg) (scripts: afpacket.zeek, extension.zeek, kafka.zeek, fsf.zeek, extract_files.zeek, local.zeek)
                              zeek > fsf (port 5800) (output: rockout.log) (config: opt/fsf/fsf-server/conf/config.py, opt/fsf/fsf-client/conf/config.py)
                                    fsf/suricata > filebeat (filebeat.yml)
                                              zeek/filebeat > kafka  (9092) (/etc/kafka/server.properties) (zeek-raw, fsf-raw, suricata-raw)
                                              zeek > zookeeper  (2181) (/etc/zookeeper/zzo.cfg)
                                                      zookeeper > logstash (9600) (/etc/logstash/conf.d/logstash-100-input-kafka-(suricata/zeek/fsf).conf) (output: logstash-9999-output-elasticsearch.conf) (logstash-500-filter-zeek-common.conf)
                                                                  logstash > elasticsearch (9200, 9300) (/etc/elasticsearch/elasticsearch.yml)
                                                                            elasticsearch > kibana (5601) (/etc/kibana/kibana.yml)

##troubleshoot network

ip a
  1 enp5s0, promisc, up
  2 eno1 172.16.50.100, up,

PING can my box talk to gateway? Can it talk to a sensor?

Socket Status
  sudo ss -lnt
  should have ip address:port
  172.16.50.100: (9200/9300/1234/5800/9092/5601

firewall
  sudo firewll-cmd --add-port=XXXX/tcp --permanent
  sudo firewall-cmd --reload
  sudo firewall-cmd --list-all
    9092, 2181, 9200, 9300, 5601
      sudo firewall-cmd --remove-port=XXXX/tcp
      sudo firewall-cmd --add-port=XXXX/tcp

Logs
  sudo journalctl -xeu


Logs (if logs arent going to elasticsearch)
  /var/log/
  /var/log/logstash     may contain logs about what is happening to logstash
    sudo tail -f /var/log/logstash/logstash-plain

TroubleShooting services
  sudo systemctl <action> <service>
    status (suricata/)
    restart and status every service after a conf is fixed

Sensor Checklist
  On repo


Verify stenographer writing to disk
  ping 192.168.2.20
  stenoread host '192.168.2.20' -nn
    writes to /data/stenographer/packets
    watch ls -al

Suricata
  sudo systemctl status suricata
  curl -LO 192.168.2.20:8080/all-class-files.zip
    /data/suricata/eve.json
    cat eve.json | jq

Kafka
  status kafka
  sudo /usr/share/....--bootstrap...--list (find in notes)
  sudo /usr/share.....bootstrap....--from-beginning (find)
    /data/kafka     to find zeek-raw and fsf-raw
    watch ls -la    see if checkpoints are being written
    sudo /usr/share/kafka/bin/kafka-topics.sh --bootstrap-server 172.16.50.100:9092 --list
    sudo /usr/share/kafka/bin/kafka-topics.sh --bootstrap-server 172.16.50.100:9092 --describe --topic zeek-raw
    sudo /usr/share/kafka/bin/kafka-console-consumer.sh --bootstrap-server 172.16.50.100:9092 --topic zeek-raw

zeek
  status zeek
  curl 192.168.2.20:8080
  /data/zeek/current
  cat conn.log
  /data/zeek/extracted_files

FSF
  create a test file (touch > vi)
  /opt/fsf/fsf-client/fsf_client.py --full <filename>
  cd /data/fsf/logs           (looking for rockout)
  cat rockout.log | jq

Elasticsearch
  curl 172.16.50.100:9200
    should return node and stuff

Kibana
  go to kibana on web browser then discover
  create and indecies
  Dev tools

Filebeat
  same bootstrap command for fafka (suricata and fsf -raw)

Logstash
  validate logstash log to ensure piepline started
  -sudo tail -f /var/log/logstash/logstash-plain

  validate indices are being created
  watch -d curl <sensor ip>:9200/_cat/indices?v
