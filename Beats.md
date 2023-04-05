#BeatsByDre

  Single purpose lightweight data shipper
  echo $beat
  Using Filebeat


##Install

ssh in
sudo yum install filebeat-7.8.1
y
sudo mv /etc/filebeat/filebeat.yml /etc/filebeat/file.yml.bk
cd /etc/filebeat
sudo curl -LO http://192.168.2.20:8080/filebeat.yml
sudo vi /etc/filebeat/filebeat.yml
  :set nu
  line 11-18
  EVERYTHING SHOULD lINE UP WITH THE - FROM THE FIRST SECTIONS
```
  - type: log
    enabled: true
    paths:
      - /data/fsf/logs/rockout.log
    json.keys_under_root: true
    fields:
      kafka_topic: fsf-raw
    fields_under_root: true

```
  line 22 change local host to 172.16.50.100:9092
  esc
  :wq

sudo systemctl start filebeat
^start^status
sudo -s
sudo /usr/share/kafka/bin/kafka-topics.sh --bootstrap-server 172.16.50.100:9092 --list
    sudo rm -rf /usr/var/filebeat/registry    <wipe filebeats memory

  Test suricata and fsf
    sudo /usr/share/kafka/bin/kafka-console-consumer.sh --bootstrap-server 172.50.50.100:9092 --topic suricata-raw --from-beginning
    sudo /usr/share/kafka/bin/kafka-console-consumer.sh --bootstrap-server 172.50.50.100:9092 --topic fsf-raw --from-beginning

cd
