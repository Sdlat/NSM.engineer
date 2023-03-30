#Repository

    still in ssh
cd /etc/yum.repos.d
  ls -l
  sudo rm -r CentOS-*

  #open a browser
  192.168.2.20
  go to share folder
  local.repo

  back in terminal
  sudo curl -LO http://192.168.2.20:8080/local.repo
  verify repo is correct
  sudo yum makecache
    should see local- a bunch of stuff
  sudo yum list zeek
    should show available packages
  sudo yum update
  y
  sudo systemctl reboot

  ssh admin@172.168.50.100
  cd /etc/yum.repos.d
  ls -l
  sudo rm -rf CentOS-*



  sudo yum makecache --disablerepo="*" --enablerepo=local"*"
  sudo yum update --disablerepo="*" --enablerepo=local"*"
  sudo systemctl reboot

  ssh admin@172.16.50.100
  cd /etc/yum/repos.d
  ls -l
    if centos repos are back kill them
