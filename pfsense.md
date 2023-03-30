Set up

plug in usb, vga, keyboard
spam f11 during start up
click uefi 2.0
  if doesn't work 5 > y> enter > f11

  blue screen
  accept
  install
  default map
  uefi
  no
  reboot
  pull out the usb

1 assign interface
n
em0 for wan
em1 for lan
leave blank, enter
y

after reload 2
2
172.16.50.1
24
172.16.50.1
enter
y
172.16.50.100
172.16.50.254
y

Change Ip:
  touch triple box spot top right of computer
  network
  wired gear
  ipv4
  address ipv4 172.16.50.200
  gateway 172.16.50.1

  log in to pfsense
  url: 172.16.50.1
  username: admin
  pass: pfsense

  set up wizard
  next
  next
  hostname: sg420
  domain: empty
  primary dns: 192.168.2.1
  overide dns checked
  next
  next
  uncheck bottom two boxes
  next
  next
  next
  reload
  finish

  firewall
  rules
  add
  make rule
  save
  apply changes

  Services
  dns forwarder
  uncheck dns forwarder
  save
  apply changes

  diagnostic
  halt system
  halt
  ok
