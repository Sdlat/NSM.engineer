#Zeek

  zeek configuration:
    /usr/bin            binaries
    /etc/zeek           configs
    /var/log            data
    /usr/share/zeek     scripts

    /etc/zeek
      node.cfg          default=standalone
      networks.cfg      local networks
      zeek.cfg

ssh admin@172.16.50.100
sudo yum install zeek zeek-plugin-kafka zeek-plugin-af_packet
y
  sudo vi /etc/zeek/networks.cfg    <to make changes, we aren't right now
sudo vi /etc/zeek/zeekctl.cfg
  :set nu
  line 67 change /var/log/zeek to /data/zeek
  line 76 add "lb_custom.InterfacePrefix=af_packet::"
  esc
  :wq

sudo vi /etc/zeek/node.cfg
  line 8-11 comment out (single #)
  line 16-23 get rid of the #'s'
  line 23 add 'pin_cpus=1'
  line 25 - 32 get rid of #
  line 32 change eth0 to enp5s0
  line 33 add lb_method=custom
  line 34 add 'lb_procs=2'
  line 35 add "pin_cpus=2,3"
  line 36 add 'env_vers=fanout_id=77'
  esc
  :wq

sudo mkdir /usr/share/zeek/site/scripts
cd /usr/share/zeek/site/scripts
sudo curl -L -O http://192.168.2.20:8080/zeek_scripts/afpacket.zeek
ll   double check
vi afpacket.zeek      should be a single line
:q

sudo cd /usr/share/zeek/site/scripts/
ll        should see afpacket.zeek
sudo curl -L -O http://192.168.2.20:8080/zeek_scripts/extension.zeek
sudo vi extension.zeek
:q    should start talking about extensions

sudo vi /usr/share/zeek/site/local.zeek       starts with local site policy
    *we could download all scripts and add them to this vi*
  :set nu
  shift+g     gets to bottom
  i
  enter enter
  line 104 '@load ./scripts/afpacket.zeek'
  line 105 '@load ./scripts/extension.zeek'
  esc
  :wq

cd /data/
sudo chown -R zeek: /etc/zeek
sudo chown -R zeek: /data/zeek
sudo chown -R zeek: /usr/share/zeek
sudo chown -R zeek: /usr/bin/zeek
sudo chown -R zeek: /var/spool/zeek
sudo /sbin/setcap cap_net_raw,cap_net_admin=eip /usr/bin/zeek
sudo /sbin/setcap cap_net_raw,cap_net_admin=eip /usr/bin/capstats
sudo getcap /usr/bin/zeek       should show we are good
sudo getcap /usr/bin/capstats

sudo vi /etc/systemd/zeek.service       creating this from scratch
  ```
[Unit]
Description=Zeek Network Analysis Engine
After=network.target

[Service]
Type=forking
user=zeek
ExecStart=/usr/bin/zeekctl deploy
ExecStop=/usr/bin/zeekctl stop

[Install]
WantedBy=multi-user.target

  ```
  esc
  :wq

cd /usr/share/zeek/site/scripts/
    sudo vi /usr/share/zeek/site/local.zeek        check bottom for load of scripts
sudo systemctl daemon-reload        can do this anywhere
sudo systemctl start zeek


sudo ls -l /data/zeek/current/      conn.log is the most important to have, should have 68-92
sudo systemctl enable zeek
