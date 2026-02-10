
	Born2BeRoot	Short Instructions

(1) Installing sudo:
	su
	apt install sudo
	sudo reboot
	exit
	sudo -V
	groups
	groups <username>
	getent group user42 sudo
	cat /etc/group

	sudo adduser <username>
	sudo addgroup user42
	sudo adduser <username> <group>
	sudo usermod -aG user42 <username>
	passwd
	sudo passwd <username>
	whoami

(2) Installing SSH
	sudo apt install openssh-server
	sudo service ssh status
	sudo nano /etc/ssh/sshd_config
	(Port 4242, PermitRootLogin no)

	sudo service ssh restart
	sudo service ssh status
	ssh <username>@localhost -p 2424











(3) Firewall configuration:
	sudo apt install ufw
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	sudo ufw allow 4242/tcp
	sudo ufw enable
	sudo ufw status verbose
	sudo systemctl status ufw
	sudo systemctl restart ufw

	sudo ufw status numbered
	sudo ufw delete 1
	sudo ufw delete allow 4242/tcp
	sudo ufw reload	

(4) Sudoers configuration:
	/etc/sudoers
	/etc/sudoers.d/sudo_config
	sudo visudo
	sudo visudo -f /etc/sudoers.d/sudo_config
	sudo chmod 440 /etc/sudoers.d/sudo_config

	/var/log/sudo/sudo.log
	sudo mkdir -p /var/log/sudo
	sudo chmod 750 /var/log/sudo

	sudo visudo -c
	sudo ls /root
	sudo cat /var/log/sudo/sudo.log

(5) Password policy:
	sudo chage -l <username>
	sudo nano /etc/login.defs
	sudo chage -M 30 -m 2 -W 7 <username>
	sudo chage -M 30 -m 2 -W 7 root

	sudo apt install libpam-pwquality
	sudo nano /etc/pam.d/common-password
	


(6) Crontab and script
	sudo nano /usr/local/bin/monitoring.sh
	sudo chmod +x /usr/loca/bin/monitoring.sh
	sudo /usr/local/bin/monitoring.sh
	
	sudo crontab -e
	*/10 * * * * /usr/local/bin/monitoring.sh
	sudo crontab -l

	sudo systemctl status cron
	sudo systemctl enable cron
	sudo systemctl start cron
	sudo systemctl stop cron
	sudo systemctl disable cron


(miscellaneous)
	
	lsblk

	sudo shutdown now
	
	dpkg -l | grep apparmor
	sudo systemctl status apparmor
	sudo aa-status
	sudo apt install apparmor
	
	hostname
	hostnamectl
	cat /etc/hostname
	cat /etc/hosts
	sudo hostname <new_hostname>
	sudo hostname set-hostname <newhostname>
	
	sha1sum <machinename>.vdi

	sudo apt update
	sudo apt upgrade

	AppArmor is restrict what programs are allowed to do.