#SELinux + Snipping Traffic + Tap

ssh admin@172.16.50.100
sestatus   want to see selinux enabled


#Snipping Traffic

(still ssh'd in)
ip a
sudo ethtool -k enp5s0
  we want receive and transmit off (interface_prep.sh to get rid of)
  cd ..
  cd /home
  sudo curl -LO http://192.168.2.20:8080/interface_prep.sh
  ll to check
  cat interface_prep.sh
    should see it turning everything off
    last line sets promisc on
  sudo chmod +x interface_prep.sh
  ll
  sudo ./interface_prep.sh enp5s0
  sudo ethtool -k enp5s0

  cd /sbin
  sudo curl -LO http://192.168.2.20:8080/ifup-local
  sudo chmod +x ifup-local
  cd /etc/sysconfig/network-scripts/
  sudo vi ifup
    go to the bottom of the script, enter insert mode
```
if [ -x /sbin/ifup-local ]; then
        /sbin/ifup ${DEVICE}
fi
```    esc
      :wq

    *Still in network-scripts
      sudo vi ifcfg-enp5s0
        changes:
        Bootproto=none
        *all* ipv6=no       <leave ipv6_addr_gen_mode=stable
        defroute=none
        onboot = yes
          Add to the end:
          NM_CONTROLLED=no
        esc
        :wq
      sudo systemctl reboot

    ssh admin@172.16.50.100
    sudo ethtool -k enp5s0
      Want to see that the checksum stayed off
    ip a
      enp5s0 should be in promisc mode

#Tap
  connect tap between sensor and pfsense
  monitor on tap to snipping interface on nook (left ethernet)
  nook right ethernet, disconnect on lan 2 of pfsense, connect to port 1 on tap
    so right side of nook now connects to port 1 of tap
    add usb to any usb on nook to power
  eth port 2 on tap to lan2 on pfsense

   ssh admin@172.16.50.100
   cd /etc/yum/repos.d
   ll
   sudo yum install tcpdump --disablerepo=* --enablerepo=local*
   y
   sudo yum list tcpdump

   sudo tcpdump -i enp5s0
    can control c to stop
    sudo tcpdump -nn -i enp5s0 is preferred over not -nn
    sudo tcpdump -nn -i enp5s0 -c4 is even better
    sudo tcpdump -nn -c5 -i enp5s0 '!port 22'     filter out ssh traffic
    
