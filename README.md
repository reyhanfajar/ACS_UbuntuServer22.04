# ACS_UbuntuServer22.04
## Instalasi Apache Cloudstack 4.17 pada Ubuntu Server 22.0.4 LTS
Oleh Kelompok 4: Reyhan Fajar Pamenang 2006577486, Rifqi Hari Putranto 1806200192, Syamsul Erisandy Arief 2006577611

Link Panduan Youtube: https://www.youtube.com/watch?v=86e4bFxHuig

Untuk konfigurasi ini menggunakan username: kelompok4cc dan alamat IP 192.168.10.44

## Perintah konfigurasi pada Ubuntu Server
```
apt update
apt-get update
apt-get upgrade
wget https://github.com/muesli/duf/releases/download/v0.6.0/duf_0.6.0_linux_amd64.deb
dpkg -i duf_0.6.0_linux_amd64.deb
duf
apt install htop lynx net-tools bridge-utils uuid openntpd intel-microcode -y (htop taskman lynx browser)
nano  /etc/ssh/sshd_config -> permitrootlogin yes
ifconfig
```
## Pada Client, buka cmd, lalu masukkan perintah:
```
ssh kelompok4cc@[Alamat Jaringan yang digunakan]
```
## Lalu ketik yes, setelah itu Client akan terhubung ke Ubuntu Server. Dari sini, konfigurasi dapat dilanjutkan.
```
sudo su
pass 
cd /etc/netplan
nano 00-installer-config.yaml
```
## Pada konfigurasi netplan, tambahkan bridge cloudbr0 dengan alamat 192.168.10.44, -to: default = 0.0.0.0/0 via: 192.168.10.1 sesuai jaringan yang dimiliki
```
  bridges:
    cloudbr0:
      addresses: [192.168.10.44/24]
      routes:
       - to: 0.0.0.0/0
         via: 192.168.10.2
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
      interfaces: [ens33]
      dhcp4: false
      dhcp6: false
      parameters:
        stp: false
        forward-delay: 0
```
## Setelah itu, terapkan perubahan pada Netplan
```
cd /
netplan generate
netplan apply
reboot
```
## Pada Client, login ulang melalui SSH seperti pada langkah sebelumnya, namun menggunakan alamat IP cloudbr0
```
ssh login ssh kelompok4cc@192.168.10.44
sudo su
pass
```
## Konfigurasi Apache Cloudstack
```
mkdir -p /etc/apt/keyrings
wget -O- ...
echo deb
apt
apt-get update -y
apt-get install cloudstack-management mysql-server -y

nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
```
[mysqld]
server-id = 1
sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ERROR_FOR_DIVISION_BY_ZERO,NO_ZERO_DATE,NO_ZERO_IN_DATE,NO_ENGINE_SUBSTITUTION"
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=1000
log-bin=mysql-bin
binlog-format = 'ROW'
```
```
systemctl restart mysql
cloudstack-setup-databases kelompok4cc:kelompok4cc@192.168.10.44 --deploy-as=kelompok4cc:[PASSWORD USER] -i 192.168.10.44
apt-get install nfs-kernel-server quota
echo "/export  *(rw,async,no_root_squash,no_subtree_check)" > /etc/exports
mkdir -p /export/primary /export/secondary
exportfs -a
sed -i -e 's/^RPCMOUNTDOPTS="--manage-gids"$/RPCMOUNTDOPTS="-p 892 --manage-gids"/g' /etc/default/nfs-kernel-server
sed -i -e 's/^STATDOPTS=$/STATDOPTS="--port 662 --outgoing-port 2020"/g' /etc/default/nfs-common
echo "NEED_STATD=yes" >> /etc/default/nfs-common
sed -i -e 's/^RPCRQUOTADOPTS=$/RPCRQUOTADOPTS="-p 875"/g' /etc/default/quota
service nfs-kernel-server restart
apt-get install qemu-kvm cloudstack-agent
sed -i -e 's/\#vnc_listen.*$/vnc_listen = "0.0.0.0"/g' /etc/libvirt/qemu.conf
```
## Pada /etc/default/libvirtd, tambahkan 
```
LIBVIRTD_ARGS="--listen" to /etc/default/libvirtd
```
```
UUID=$(uuid)
echo host_uuid = ...
echo 'listen_tls=0' >> /etc/libvirt/libvirtd.conf
echo 'listen_tcp=1' >> /etc/libvirt/libvirtd.conf
echo 'tcp_port = "16509"' >> /etc/libvirt/libvirtd.conf
echo 'mdns_adv = 0' >> /etc/libvirt/libvirtd.conf
echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf
systemctl restart libvirtd
ln -s /etc/apparmor.d/usr.sbin.libvirtd /etc/apparmor.d/disable/
ln -s /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper /etc/apparmor.d/disable/
apparmor_parser -R /etc/apparmor.d/usr.sbin.libvirtd
apparmor_parser -R /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper
cloudstack-setup-management
```
Setelah ini, cloudstack sudah dapat diakses melalui alamat 192.168.10.44:8080/client. Untuk setup bisa dilakukan dengan mengikuti perintah berikut:
## Zone
```
Name - Zone1
Public DNS 1 - 8.8.8.8
Internal DNS1 - [gateway router]
Hypervisor - KVM
```
## Network
```
Gateway - 192.168.10.1
Netmask - 255.255.255.0
Start IP - 192.168.10.20
End IP - 192.168.10.50
```
## Pod
```
Name - Pod1
Gateway - 192.168.10.1
Start/end reserved system IPs - 192.168.10.55 - 192.168.10.90
```
## Guest Traffic
```
VLAN/VNI range: 700-900
```
## Cluster
```
Name - Cluster1
Hypervisor - Choose KVM
```
## Host
```
Hostname - 192.168.10.44
Username - komputasiawan
Password - [PASWWORD USER]
```
### Primary Storage
```
Name - Primary
Scope - Zone
Protocol - NFS
Server - 192.168.10.44
Path - /export/primary
```
## Secondary Storage
```
Provider - NFS
Name - Secondary
Server - 192.168.10.44
Path - /export/secondary
```
## Setelah itu, penerapan Instance dapat dilakukan melalui menu Instances, dan mengikuti petunjuk yang ada.
