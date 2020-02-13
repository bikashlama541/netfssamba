## NFS: Network File System
* It is a way of mounting directories over a network. 
* In a typical NFS setup there is a server that exports a directory in any number of clients

## Steps:
* On the server side
* mkdir /nfs in a CentOS instance
* chmod 777 /nfs to open it up to the world
* vim /etc/exports
* write /nfs *(rw) just gives read write permission to the world 
* Install nfs-utils using yum install -y nfs-utils
* systemctl start {rpcbind,nfs-server,rpc-statd,nfs-idmapd}
* exportfs -a
* showmount -e localhost will show the directory that we are exorting 

#### Also we should open the ports for the server using following firewall commands 
* firewall-cmd --permanent --add-service=rpc-bind
* firewall-cmd --permanent --add-service=mountd
* firewall-cmd --permanent --add-port=2049/tcp
* firewall-cmd --permanent --add-port=2049/udp
* firewall-cmd --reload
* semanage port -l | grep 2049 returns SElinux xontext and the service name 
* netstat -tulpen | grep 2049 

## On the client side 
* systemctl start rpcbind 
* rpm -q nfs-utils to see if we have nfs package
* showmount -e server ip 
* mkdir /mnt/nfs
* mount -t nfs serverip:/nfs /mnt/nfs
* cd /mnt/nfs
* touch file 
* After doing ls -la we see that owner and group that was written in the 
* client server's file is nfsnobody because the root has been squashed
* this is for security reasons as nobody should have access in the 
 server. 

## SMB 
* Server Message Block is a protocol that can be used to share access to files,printers, and other resources
* Name of the protocol has changed to cifs(common internet File system), many packages still use the SMB terminology . 

On the server
yum install samba -y
mkdir /smb
chmod 777 /smb
vim /etc/samba/smb.conf
Type [share]
        browseable = yes
        path = /smb
        writeable = yes at the very bottom 
useradd shareuser 
smbpasswd -a shareuser
systemctl start smb
getenforce checking seLinux should be turned off
setenforce 0

On the client
yum install cifs-utils samba-client -y
mkdir /mnt/smb
mount -t cifs //serverip/share /mnt/smb/ -ousername=shareduser,password=abcdef
in the client use  firewall-cmd --list-ports to see if the samba ports are open 
Since the ports won't be open we will have to manually open the ports 
use firewall-cmd --add-service=samba
firewall-cmd --add-service=samba --permanent
To verify : 
firewall-cmd --list-services 


