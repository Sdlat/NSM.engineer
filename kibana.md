#Kibana

##install

still ssh in to 172.168.50.100

sudo yum install kibana-7.8.1
y
sudo vi /etc/kibana/kibana.yml
  line 7 to 'server.host: "172.16.50.100"'
  line 28 to 'elasticsearch.hosts: ["http://172.16.50.100:9200"]'
sudo systemctl start kibana
^start^status
^status^enable
sudo firewall-cmd --add-port=5601/tcp --permanent
sudo firewall-cmd --reload
ss -lnt
  in a browser 172.16.50.100:5601 to get into kibana
cd                    need to be in home directory for extraction
curl -LO http://192.168.2.20:8080/ecskibana.tar.gz
ll
tar -zxvf ecskibana.tar.gz
cd ecskibana
ll
sudo vi import-index-templates.sh
  Change localhost to 172.16.50.100
./import-index-templates.sh           should change 6

  Go back into kibana browser
  go to dev tools (in the hamburger on left)
  'GET _cat/templates?v'
  press the play button next to the wrench, we should see the 6 things we changed (default, ecs_mapping, ecs_suricata, ecs_zeek, lim-history)

    Not needed but good to know
    GET _cat/templates?v&s=name   <slightly easier to read version
    can then get specific template via <enter><enter> GET_template/(default, ecs_mapping, ecs_zeek)
    Would look like:
    ```
    cat/templates?v&s=name

    GET_template/ecs_zeek

    ```

  go to stack management then index patterns. Should have nothing in it
