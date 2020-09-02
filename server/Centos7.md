Centos7
========================

**Update and install software**  

	yum update && yum -y install epel-release && \  
	yum -y install htop atop mlocate mc wget curl fail2ban vim certbot net-tools vsftp.d db4-utils mc zip unzip
	

**Setup firewall**

*Assume that ssh port is 29920 instead of 22, opening ftp and web-server ports*

	
	 yum -y install firewalld && \
	 systemctl start firewalld && \
	 systemctl enable firewalld && \
	 firewall-cmd --permanent --zone=public --add-service=http &&
	 firewall-cmd --permanent --zone=public --add-service=https &&
	 firewall-cmd --permanent --zone=public --add-port=443/tcp &&
	 firewall-cmd --permanent --add-port=21/tcp &&
	 firewall-cmd --permanent --add-service=ftp &&
	 firewall-cmd --permanent --add-port=29920/tcp &&
	 firewall-cmd --reload

in case of selinux:
`sudo semanage port -a -t ssh_port_t -p tcp 29920`
## **Setup SSH**
### Create a new user

  
	username=yourname  
	adduser $username
	passwd $username  

a) adduser under the root in /etc/sudoers to give him sudo access  

	visudo
	
> root		ALL=(ALL)       ALL  
> yourname		ALL=(ALL)       NOPASSWD:ALL

b) add user to the common group for sudo users  
make sure this group is uncommented in /etc/sudoers

	usermod -aG wheel $username

### Change ssh config  
prevent root from entering, changing ssh port, allowing new user to enter
	
	sed -i 's/^.*Port .*/Port 29920/g' /etc/ssh/sshd_config && \
	sed -i 's/^.*PermitRootLogin yes/PermitRootLogin no/g' /etc/ssh/sshd_config && \
	echo -e "\nAllowUsers $username" >> /etc/ssh/sshd_config && \
	systemctl restart sshd
	
### Check if you can connect in the new ssh window  
	ssh -p29920 yourname@domain
 
### Check if you have a root  

	sudo -i 
  
generate public key on client and push it to the server  

	ssh-keygen  
`cat /home/user/.ssh/id_rsa.pub >> yourname@domain:/home/user/.ssh/authorized_key`  

or just  

`ssh-copy-id name@domain`

### Add notification on root login 
	vi .bashrc  
> echo 'ALERT - SSH root shell access found on '$HOSTNAME' on:' `date` `who` | mail -s "Alert: SSH root shell access" your@mail.com
