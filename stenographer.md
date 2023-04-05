Google stenographer
  uses first in first out
  fast write speed slow read speed
  stenotype writes to disk, adds 4 byte header to index and search packet
  stenoread uses queries to retrieve packets from index

ssh admin@172.16.50.100
sudo yum list stenographer
  should be pulling from local rocknsm
sudo yum install stenographer
y
cd /etc/stenographer
ll
  should have certs and config
sudo vi config
  changefirst line to PacketsDirectory: "/data/stenographer/packets"
  change second line to , "indexDirectory": "/data/stenographer/index"
  change stenotype path to just /bin/stenotype
    sudo which stenotype to find where the above path is
  change interface to enp5s0
  esc
  :wq

sudo mkdir /data/stenographer/packets
sudo mkdir /data/stenographer/index
cd /data/stenographer
cd ..
sudo chown -R stenographer:stenographer /data/stenographer

sudo stenokeys.sh stenographer stenographer
sudo systemctl start stenographer
sudo systemctl status stenographer
  should be active
sudo systemctl enable stenographer
  ^enable^status    <this replaces a word in a command
    sudo journalctl -xeu stenographer    <troubleshooting command
ping 192.168.2.20
sudo stenoread 'host 192.168.2.20' -nn
  sudo stenoread 'host 192.168.2.20' -nn 'src host'   <specifies direction
    cd /data/stenographer   <used to verify packets coming through
    cd packets
    ll
    ls -al
    
