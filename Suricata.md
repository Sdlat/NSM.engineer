#Suricata
  *after stenographer*

(still ssh into 172.16.50.100)
sudo yum list suricata
sudo yum install suricata    
y
sudo -s
cd /etc/suricata
ll
  should see suricata.yaml
vi suricata.yaml
  :set nu
    line 16 should be 192.168.0.0/16, /8 and /12
  go to line 56
    change /var/log/suricata/ to /data/suricata
  go to line 60
   change enabled: yes to enabled: no
  Go to line 76
    change enabled: yes to enabled: no
  line 83 should be yes
  go to line 404
    change enabled: yes to enabled: no
  go to line 557
    change to enabled: no
  go to line 580
    change interface: eth0 to interface: enp5s0
  go to line 582
    remove the #
  esc
  :wq

vi /etc/sysconfig/suricata
  i
    change last line to: OPTIONS="--af-packet=enp5s0 --user suricata "
  esc
  :wq
sudo suricata-update add-source local-emerging-threats http://192.168.2.20:8080/emerging.rules.tar
sudo suricata-update
  to check sources you have: sudo suricata-update list-sources

cd /data
ll
sudo chown -R suricata: /data/suricata
ll
systemctl start suricata
systemctl enable suricata
^enable^status
  trying to trigger some alerts
curl -LO 192.168.2.20:8080/all-class-files.zip
cd /data/suricata
ll
  should see eve.json, if no eve.json check suricata yaml
cat eve.json | jq
