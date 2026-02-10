*This project has been created as part of the 42 curriculum by sapuszta.*



######################## Description: #################################

Born2BeRoot pjoect is system administration project.
The goal is to teach the students the Linux system administration,
security practices and virtualization.

It's about creating a Linux based virtual machine, and setting up, 
as a server while following the instrucions in the subject.pdf.

This project require to setting up a Debian or Rocky Linux server, including:
- Disk partitioning and management
- User and group management
- SSH configuration
- Firewall configuration
- Sudo privilages
- Security polices and services
- Crontab and monitoring script

------------------------------- OS choise -----------------------------

I have chosen Debian for this project because:
- it's stable and reliable
- It's have large community where I can get help
- Simpler for beginners in system administration
- Supporting AppArmor wich is easier to configure than SELinux

Pros:
- Very stable and well tested
- Lightweight and efficient
- Uses apt as an excellent package management system
- Beginner friendly

Cons:
- Slower release cycle
- User slightly older package versions

This is acceptable becuse stability is more important than the updates.


---------------------------- Main design choices ----------------------

Disk partitioning:
- LVM (Logical Volume Manager) was used.
	LVM was chosen because it provides an abstraction over physical disks.
- Separate partitions for "/", "/home", "/var", "/var/log", "/tmp", "/srv", "swap", 
- Improves 
	- security: limits the impact of disk filling attacks
	- stability: prevents one directory from exhausting all disk space
	- maintainability: allows resizing logical volumes if needed

Security policies:
- AppArmor is enabled and it restricts what programs are allowed to do.
- strong password policies enforced via PAM:
	- minimum length
	- expiration rules
	- retry limits
		configured via: /etc/login.defs and /etc/pam.d/common_password
- root login via SSH is disabled
- SSH runs on a non default port
	- configured in: /etc/ssh/sshd_config
- only required services are installed

User and group management:
- a non root user "sapuszta" created
- that user added to sudo group
- sudo secure path is modified
- actions by sudo users are logged for accountability
- sudo configuration using:
	- /etc/sudoers (not edited)
	- /etc/sudoers.d/sudo_config
	- /var/log/sudo/sudo.log (for logging)

Services installed:
- virtualbox - was already installed
- apparmor - comes with Debian
- sudo
- openssh-server - for secure communication
- ufw - for restrict incoming connections
- libpam_pwquality
- cron - for scheduled system tasks, configured in: /etc/cron.d/
	


----------------------------- Comparisons -----------------------------

Debian vs Rocky Linux
Based: community support | RHEL compatible.
Stability: very high / very high.
Package manager: apt / dnf
Serurity module: AppArmor / SELinux
Lernign curve: quick / steep
Targe use: general servers / enterprise servers

Conclusion: Debian is better for learning and small and medium servers
while Rocky Linux is ideal for enterprise servers.


AppArmor vs SELinux
Configuration: path based / label based
Complexity: simple / complex
Flexibility: moderate / very high
Learnign curve: beginner friendly / advanced
Default on: Debian / RHEL based systems

Conclusion: AppArmor is an easy to use Linux Security Module implementation
that restricts applications, capabilites and permissions with profiles
that are set per program. So it's good enough for this project.


UFW vs Firewalld
Configuration: simple / advanced
Dynamic rules: no / yes
Learing curve: easy / moderate
Default on: Debian / RHEL based

Conclusion: UFW is simpler and ideal for small systems.


VirtualBox vs UTM
Platform: cross platform / macOS
Performance: very good / optimized for macOS
Compatibilyt: widely supported / limited
At 42: standard / optional

Conclusion: Virtualbox is prefered due to wide compatibility and support.


Apt vs Aptitude
Type: CLI package manager / CLI package manager + Text based UI
Interface: CLI only / CLI + interactive text interface
Language: CLI commands / sightly more advanced commands and menus and searches
Purpose: install, remove, update packages / same but more advanced dependency resolution
Default: default on Debian / default optional, sometimes installed by default

Conclusion: apt is simplier and ideal for daily tasks, while aptitude can solve
dependency conflicts and help in package selection.



########################### instructions:##############################


-------------- Installing Linux to VBox and partitioning --------------

1. Create a virtual machine in VirtualBox.

2. Install Debian (minimal installation, no GUI, no extra services).

3. Partition the disk using LVM:
   - Create separate partitions for: /, /home, /var, /var/log, /tmp, /srv, swap
   - Configure logical volume groups and logical volumes

4. Configure system settings:
   - Hostname
   - Root and user accounts
   - Set passwords and passphrase


Notes:
- VirtualBox is already installed on the campus computer.
- I created a folder in /sgoinfre/<username> to store VM files.
- Downloaded the Debian ISO (amd64).
- Partitioning is done without using the GUI.


sapuszta@sapuszta42:~$ lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINTS
sda                    8:0    0   30G  0 disk  
├─sda1                 8:1    0  476M  0 part  /boot
├─sda2                 8:2    0    1K  0 part  
└─sda5                 8:5    0 29.5G  0 part  
  └─sda5_crypt       254:0    0 29.5G  0 crypt 
    ├─Group-root     254:1    0  9.3G  0 lvm   /
    ├─Group-swap     254:2    0  2.1G  0 lvm   [SWAP]
    ├─Group-home     254:3    0  4.7G  0 lvm   /home
    ├─Group-var      254:4    0  2.8G  0 lvm   /var
    ├─Group-srv      254:5    0  2.8G  0 lvm   /srv
    ├─Group-tmp      254:6    0  2.8G  0 lvm   /tmp
    └─Group-var--log 254:7    0  3.7G  0 lvm   /var/log
sr0                   11:0    1 1024M  0 rom  



In the following sections I have written down "alsmost" all the commands and steps 
that I have used for this project:

------------------------ User and Groups (1) --------------------------

Sudo is a command that can execute other command what root or "superuser do".
This is important because it gives the sudo user higher privilages than the 
normal user status, but does not give unlimited access as root does.

(1) Installing sudo

switch to root user:
	su

install sudo (in root user):
	apt install sudo
	sudo reboot

switch back or switch user:
	exit
	sudo su - <otheruser>

verify:
	sudo -V
	groups


(2) Crating a user and a group

add a user:
	sudo adduser <username> (username is a login)

add a group:
	sudo addgroup user42 (user42 is a group)

add user to groups
	sudo adduser <username> <group>
	sudo adduser <username> user42
	sudo adduser <username> sudo
	sudo usermod -aG user42 <username>

verify:
	groups <username>
	getent group user42
	getent group user42 sudo
	cat /etc/group


(3) Change passwd

change own passwd:
	passwd

change other user passwd:
	sudo passwd <username>


(4) Delete a user

delete user without delete home directory:
	sudo deluser <username>

delete user and its home directory (chose one):
	sudo deluser --remove-home <username>
	sudo userdel -r <username> (low level)

verify user deletion:
	getent passwd <username>
	(no output means success)

delete user from groups:
	sudo deluser <username> sudo
	sudo deluser <username> user42


(5) Check if you are in sudo group:

	whoami
output: username
	
	sudo whoami
output: root

note:
	(user can use the sudo command for 15 mins by default)



----------------------------- SSH (2) ---------------------------------

SSH is a secure shell. It is able to communicate through a "tunnel", 
which is encrypted.

(1) Installing SSH

update the system:
	sudo apt update

install OpenSSH server:
	sudo apt install openssh-server

verify:
	sudo service ssh status
	(active is good)


(2) Configuring SSH

editing SSHD config:
	sudo nano /etc/ssh/sshd_config

modify the config:
	find:      "#Port 22"
	change to: "Port 4242"

disable root login via SSH:
	find:      "#PermitRootLogin prohibit-password"
	change to: "PermitRootLogin no"

restart and verify:
	sudo service ssh restart
	sudo service ssh status

(3) Login from the host:
	ssh <username>@<vm-ip> -p <port>
	ssh <username@127.0.0.1 -p <port>
	ssh <username>@localhost -p 2424

(4) To change the hostname:
	sudo hostname <new hostname>
	

Configure network for SSH access (port 4242)
	To able to communicate the host and the guest machine on the virtualbox
	port forwarding option I did setup port 2424 for the host and port 4242
	for the guest, it is because of a conflict if I use 4242 on the host.



---------------------------- Firewall (3) -----------------------------

Firewall is limiting the ports where the server can communicate 
with the outside world.

(1) Firewall configuration (UFW - Uncomplicated Firewall)

install:
	sudo apt update
	sudo apt install ufw

set default rules:
	sudo ufw default deny incoming
	sudo ufw default allow outgoing

and make an exception, by allow SSH:
	sudo ufw allow 4242
or:	sudo ufw allow 4242/tcp

enable firewall:
	sudo ufw enable

verify:
	sudo ufw status
	sudo ufw status verbose
	sudo systemctl status ufw
	
Delete a rule:
	sudo ufw status numbered
	sudo ufw delete 1
	sudo ufw delete allow 4242/tcp
	sudo ufw reload



----------------------- Sudoers Configuration (4) ---------------------

Sudoers, meaning "sudo  group", when the root can include users, and they 
will be able to execute sudo commands and gain access to files and 
dictionaries like a root does.

(1) Config location

- must be on the: /etc/sudoers (file)
- OR in the /etc/sudoers.d/ (directory) (recommended)

(2) Editing

	sudo visudo (to edit /etc/sudoers)
	sudo visudo -f /etc/sudoers.d/sudo_config
	sudo chmod 440 /etc/sudoers.d/sudo_config

(3) Required sudoers rules
	Defaults env_reset 	(in the sudoers)
	Defaults mail_badpass	(in the sudoers)

	(in the sudo_config)
	Defaults secure_path=
	"/usr/local/sbin:
	/usr/local/bin:
	/usr/sbin:
	/usr/bin:
	/sbin:
	/bin:
	/snap/bin" (all in one line)

	Defaults requiretty
	Defaults passwd_tries=3
	Defaults badpass_message="Wrong password, You bastard!"
	Defaults logfile=/var/log/sudo/sudo.log
	Defaults iolog_dir=/var/log/sudo
	Defaults log_input,log_output


(4) Create log directory (MUST)
	sudo mkdir -p /var/log/sudo
	sudo chmod 750 /var/log/sudo
	sudo chown root:root /var/log/sudo (optional)


(5) Verify configuration

(a) validate syntax:
	sudo visudo -c

expected:
	/etc/sudoers: parsed OK
	/etc/sudoers.d/sudo_config: parsed OK


(b) verify sudo permissions:

login as a normal user and run:
	sudo whoami

expected:
	root


(c) verify logging:

type:		sudo ls /root
then check:	sudo cat /var/log/sudo/sudo.log

output: Jan 25 10:00:00 user : TTY=pts/0 ; PWD=/home/user ; USER=root ; COMMAND=/bin/ls /root



-------------------------- Password Policy (5) ------------------------

Password policy is a set of rules which determine what complexity a 
password can have and how long it can be used or changed.

(1) Setup password aging

check passwd aging for a user:
	sudo chage -l <username>

(a) apply password "aging" rules (new users):
	sudo nano /etc/login.defs

	PASS_MAX_DAYS 30
	PASS_MIN_DAYS 2
	PASS_WARN_AGE 7

(Max number of days before password expires)
(Min number of days before password can be change)
(Number of days before password expiration to show warning)

(b) apply password expiration rules (per user only):
	sudo chage -M 30 -m 2 -W 7 username
	sudo chage -M 30 -m 2 -W 7 root
	(this have to be done for already existing users)

(c) verify by change the passwords:
	change own password:
		passwd
	change other user password:
		sudo passwd <username>
Passwords:
	root:	   Adminlogin1
		   Loginadmin1

	sapuszta:  userlogin
	
	sapuszta2: Userlogin1
		   Loginuser1
		
encrypted disk:	   disk

(root and sudo users can bypass "aging" and "history" rules)



(2) Setup password quality

install pwquality:
	sudo apt install libpam-pwquality

Edit PAM (Pluggable Authentication Modules) config:

apply "complexity" rules (all in one line):
	sudo nano /etc/pam.d/common-password

	password requisite 
	pam_pwquality.so 
	retry=3 
	minlen=10 
	ucredit=-1 
	dcredit=-1 
	lcredit=-1 
	maxrepeat=3 
	reject_username 
	difok=7 
	enforce_for_root

<type> <control> <module> <module-options>:

	(type) (control) (module)	
	(minimum 10 characters)
	(at least 1 uppercase)
	(at least 1 lowercase)
	(at least 1 digit)	
	(no more then 3 repeated chars)	
	(username cannot be part of the last passwd)
	(passwd must differ from the old one by 7 chars)
	(applied to root as well if its change a passwd)

validate this line exists:
	cat /etc/pam.d/common-password
	password requisite pam_pwquality.so retry=3 ...



-------------------------- Crontab (6) --------------------------------

A Cron Job is a Linux program that allows users to schedule the 
execution of a piece of software, often in the form of a shell 
script or a compiled executable. Cron is typically used when you 
have a task that needs to be run on a fixed schedule, and/or to 
automate repetitive tasks like downloading files or sending emails.

*    *    *    *    *   /home/user/bin/somecommand.sh
|    |    |    |    |            |
|    |    |    |    |    Command or Script to execute
|    |    |    |    |
|    |    |    | Day of week(0-6 | Sun-Sat)
|    |    |    |
|    |    |  Month(1-12)
|    |    |
|    |  Day of Month(1-31)
|    |
|   Hour(0-23)
|
Min(0-59)


Create the script file:
	sudo nano /usr/local/bin/monitoring.sh

Minimal example:
	#!/bin/bash
	arch=$(uname -a)
	wall "
	Architecture: $arch
	"
Make the file executable:
	sudo chmod +x /usr/local/bin/monitoring.sh

Test it:
	sudo /usr/local/bin/monitoring.sh

Configure crontab:
	sudo crontab -e

add this line:
	*/10 * * * * /usr/local/bin/monitoring.sh
	(every 10 minutes)

verify:
	sudo crontab -l

Check cron service status:
	sudo systemctl status cron

if not active:
	sudo systemctl enable cron
	sudo systemctl start cron

to stop now or disable on boot
	sudo systemctl stop cron
	sudo systemctl disable cron



----------------------------- Script (7) ------------------------------               

It's a bash script, it will start with a wall command, which is for 
sending broadcasting messages to all the logged-in users.
This script is about harvesting data from different sources from the
system, storing them in a variable, then formatting and combining them 
in the wall command. which will display just the exact data
that was requested in the subject.

Located:
	sudo nano /usr/local/bin/monitoring.sh

#!/bin/bash

#ARCH AND KERNEL VERSION
arch=$(uname -a)

#CPU PHYSICAL
pcpu=$(lscpu | awk '/Socket\(s\)/{print $2}')

#CPU VIRTUAL
vcpu=$(nproc)

#RAM USAGE
ram_total=$(free -m | grep Mem | awk '{print $2}')
ram_used=$(free -m | grep Mem | awk '{print $3}')
ram_percent=$(free -m | grep Mem | awk '{printf("%.2f"), $3/$2*100}')

#DISK USAGE
disk_total=$(df -BG --total | awk '/total/ {print $2}' | tr -d G)
disk_used=$(df -BM --total | awk '/total/ {print $3}' | tr -d M)
disk_percent=$(df -BG --total | awk '/total/ {print $5}')

#CPU LOAD
cpu_load=$(top -bn1 | awk '/Cpu\(s\)/ {print 100 - $8}')

#LAST BOOT
last_boot=$(who -b | awk '{print $3, $4}')

#LVM USE
lvm_use=$(lsblk | grep -q lvm && echo yes || echo no)

#TCP  CONNECTIONS
tcp_con=$(ss -tH state established | wc -l)

#USER LOG
users=$(w -h | wc -l)
#users=$(who | wc -l) 
#users=$(users | wc -w)
#users=$(loginctl list-sessions --no-legend | wc -l)
#users=$(loginctl list-users --no-legend ! wc -l)

#NETWORK
ip=$(hostname -I | awk '{print $1}')
mac=$(ip link | grep link/ether | awk '{print $2}')

#SUDO
sudo_cmds=$(journalctl _COMM=sudo | grep -c COMMAND)

wall "
        #Architecture: $arch
        #CPU physical: $pcpu
        #vCPU: $vcpu 
        #Memory Usage: $ram_used/${ram_total}MB ($ram_percent%)
        #Disk Usage: $disk_used/${disk_total}Gb ($disk_percent)
        #CPU load: $cpu_load%
        #Last boot: $last_boot
        #LVM use: $lvm_use
        #Connections: $tcp_con ESTABLISHED
        #User log: $users
        #Network: IP $ip ($mac)
        #Sudo: $sudo_cmds cmd
"

------------------------- Script Example Output ------------------------


Broadcast message from root@wil (tty1) (Sun Apr 25 15:45:00 2021):

	#Architecture: Linux wil 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64 GNU/Linux
	#CPU physical : 1
	#vCPU : 1
	#Memory Usage: 74/987MB (7.50%)
	#Disk Usage: 1009/2Gb (49%)
	#CPU load: 6.7%
	#Last boot: 2021-04-25 14:45
	#LVM use: yes
	#Connections TCP : 1 ESTABLISHED
	#User log: 1
	#Network: IP 10.0.2.15 (08:00:27:51:9b:a5)
	#Sudo : 42 cmd



############################# Resources ###############################

https://github.com/Vikingu-del/Born2beRoot
https://noreply.gitbook.io/born2beroot
https://github.com/chlimous/42-born2beroot_guide
https://crontab.guru/
https://www.debian.org/distrib/netinst
(and all ower the internet)
(special thanks for ChatGPT DeepSeek and Google ;)

