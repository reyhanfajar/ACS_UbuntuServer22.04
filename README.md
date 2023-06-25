# ACS_UbuntuServer22.04
ACS 4.17 on Ubuntu 22.04
------------------------------------------------------------------------------------------------------------------
Ubuntu Server Installation
------------------------------------------------------------------------------------------------------------------
buka vmware
new vm
typical
select ubuntu server iso
select storage save
select storage space
customize hardware - net nat gateway .2, cpu virtualization utk kvm
finish setup
configure vnets - nat vmnet1
launch vm
setup
reboot
login
sudo su
pass
apt update
apt-get update
apt-get upgrade
wget https://github.com/muesli/duf/releases/download/v0.6.0/duf_0.6.0_linux_amd64.deb
dpkg -i duf_0.6.0_linux_amd64.deb
duf
apt install htop lynx net-tools bridge-utils uuid openntpd intel-microcode -y (htop taskman lynx browser)
nano  /etc/ssh/sshd_config -> permitrootlogin yes
ifconfig
win: buka cmd - ssh kelompok4cc@inet ens33 yes
sudo su
pass 
cd /etc/netplan
nano 00-installer-config.yaml
add cloudbr0 address 192.168.10.44 default = 0.0.0.0/0 via 192.168.10.2 karena nat vmware gatewaynya .2
cd /
netplan generate
netplan apply
reboot
winssh login ssh kelompok4cc@192.168.10.44
sudo su
pass
Cloudstack Installation
mkdir -p /etc/apt/keyrings
wget -O- ...
echo deb
apt
apt-get update -y
apt-get install cloudstack-management mysql-server -y
conf database
deploy
storage setup
configure nfs server
setup kvm
sed -i libvirtd_opts="-l"
UUID=$(uuid)
echo host_uuid = ...
nano /etc/libvirt/libvirtd.conf
cek host_uuid
systemctl restart libvirtd
conf echoes selain tls mask selain tls
conf docker
gen id
firewall optional
yes
disable apparmour
launch server
setups: zone, network, pod, guest traffic, cluster, host, prim/secstorage
XRDP apts
tcp conf




