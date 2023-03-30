Sensor (intel nook)
repo server .20 connect to the edge Router .1
edge router connects internet left
edge router connects to wifi? right
edge router connects to the cisco switch 10.0.0.2 down
port 24 of cisco switch connects to the edge router 10.0.0.1
10.0.50.1 is our physical connection to cisco switch
switch connects to pfsense router 172.16.50.1
172.16.50.100 pfsense to Sensor

---

##setting up Sensor
need intel nook, monitor,keyboard, mouse, centos7 usb
plug in everything into nook
power on nook and hit f10 while it starts
select boot device: uefi usb general part 1
install centos7 (can test)
english continue
network and hostname
Physical setup
  left ethernet side is traffic (en5psO), right is management (eno1)
  plug ethernet into right side
  plug ethernet into pfsense on lan2

software setup
  turn ethernet on top right
  configure (bottom right)
  ipv4 settings
  method: manual
  add
  address 172.16.50.100
  netmask 255.255.255.0
  gateway 172.16.50.1
    ipv6 settings
    method ignore
    need to dsiable in command line as well
  save
  bottom left, change to sg50.local.lan    <hostname
  apply
  done top

Date and time
  region: etc
  city: coordinated universal time
  network time set to on
  done

system
  kdump
  uncheck enable kdump
  done

  security policy no profile selected

  installation destination
  click on both disks
  partitioning: automatically, and check i would like to make additional space available
  done
  delete all
  reclaim space

  click installation destination again
  check both disks
  i will configure partitioning
  (box below should still be checked)
  done
  click here to create automatically
  click on /home
  desired capacity 1G
  update settings
  should convert to 1024 MB
  click on /
  change capacity to 1 GB
  update settings
  click on + bottom left
  mount point /var/log
  desired capactiy 1G
  add mount point
  click +
  /tmp
  1G
  add mount
  +
  /var
  1G
  add mount
  +
  /var/log/audit
  1G
  add mount
  +
  /var/tmp
  1G
  add mount
  +
  /data/stenographer
  1G
  add mount
  +
  /data/suricacta
  1G
  add mount
  +
  /data/kafka
  1G
  add mount
  +
  /data/elasticsearch
  1G
  add mount
  +
  /data/zeek
  1G
  add mount
  +
  /data/fsf
  1G
  add mount

Creating volume groups
  on /
  volume group, click arrow, click new volume group
  select smaller disk (500G)
  Name: vg_os
  save
  update settings
  change the following to vg_os by clicking volume group, vg_os, update:
  After change desired capacity to respective value, do / and /data/elasticsearch last
    /tmp                    (5G)
    /var                    (50G)
    /var/log                (50G)
    /var/log/audit          (50G)
    /var/tmp                (10G)
    /home                   (50G)
    /swap                   (4G)
    /                       (blank)

  vg_data (creat new volume group, 1Tb disk, vg_data)
    /data/stenographer      (500G)
    /data/suricacta         (25G)
    /data/kafka             (100G)
    /data/zeek              (25G)
    /data/fsf               (10G)
    /data/elasticsearch     (blank)

  verify names and storage groups, click done
  accept changes

Begin installation (bottom right)
  create user
  username: admin
  password: password
  check make user administator and require password
  done
  finish configuration
  (if reboot is bottom right instead pull out usb when rebooting)
  reboot

log in with credentials
ip a
  eno1 should be static ip we set it to

#on student laptop
  open terminal
  ssh admin@172.16.50.100
      First time booting go here /home/student/.ssh/known_hosts and delete
  yes
  password
  sudo vi /etc/sysconfig/network-scripts/ifcfg-eno1
  password
    change all ipv6 options from yes to no (i to get in, )
    verify name of device to eno1 and onboot to yes
    esc key
    :wq
  sudo vi /etc/sysctl.conf
  password
    at the end of the script enter twice
    net.ipv6.conf.all.disable_ipv6=1
    net.ipv6.conf.default.disable_ipv6=1
    esc
    :wq
  sudo vi /etc/hosts
    Go to second line (ipv6 line) and dd
    :wq
  sudo sysctl -p
