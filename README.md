*This project has been created as part of the 42 curriculum by dolaniya### AppArmor vs SELinux
- AppArmor is simpler to configure and easier to understand
- SELinux is more powerful but complex and enterprise‑focused

Debian uses AppArmor, which fits this project's scope and learning objectives.

### UFW vs firewalld
- UFW is simple and easy to manage
- firewalld is more complex and commonly used in enterprise environments

UFW was chosen for clarity, simplicity, and suitability for this project.

### VirtualBox vs UTMot

## Description

Born2BeRoot is a system administration project focused on creating a secure Linux server in a virtualized environment.  
The goal of the project is to understand how a server works at a low level, how to secure it, and how to configure it following best practices.

In this project, I set up a minimal Debian server without a graphical interface. The system uses disk encryption with LVM, strict password and sudo policies, a firewall, a secure SSH configuration, and an automated monitoring script that reports system information at regular intervals.

This project emphasizes **security**, **automation**, and **understanding system behavior**, not just making things work.

---

## Instructions

### System Setup
- Create a virtual machine using **VirtualBox**
- Install **Debian (no graphical interface)**
- Enable **disk encryption** with **LVM**
- Create a non‑root user and configure sudo access
- Set up password policies using PAM

### Security Configuration
- Configure **SSH** to:
  - Use port `4242`
  - Disable root login
- Enable and configure **UFW** firewall
  - Allow only port `4242`
- Enforce strong password rules
- Use sudo instead of direct root access

### Monitoring Script
- Create a monitoring script located in:
  /usr/local/bin/monitoring.sh

- The script displays:
- CPU load
- Memory usage
- Disk usage (used/total/percentage)
- Number of logged‑in users
- TCP connections
- Number of sudo commands
- The script is executed automatically every 10 minutes using **cron**
- Output is broadcasted to all logged‑in users

To verify cron:
```bash
crontab -l
```

---

## Project Design Choices

### Operating System Choice: Debian
I chose Debian because it is stable, lightweight, and well‑documented. It is widely used for servers and is easier to configure and maintain, especially for learning system administration.

#### Pros
- Stable and reliable
- Large documentation and community support
- Lightweight and flexible

#### Cons
- Slightly older packages compared to some distributions

### Debian vs Rocky Linux
- Debian is community‑driven and flexible, ideal for learning and general server use
- Rocky Linux is enterprise‑oriented, binary‑compatible with Red Hat, and commonly used in production environments

### AppArmor vs SELinux
AppArmor is simpler to configure and easier to understand

SELinux is more powerful but complex and enterprise‑focused

Debian uses AppArmor, which fits this project’s scope and learning objectives.

UFW vs firewalld
UFW is simple and easy to manage

firewalld is more complex and commonly used in enterprise environments

UFW was chosen for clarity, simplicity, and suitability for this project.

### VirtualBox vs UTM
- VirtualBox is cross‑platform and widely used at 42
- UTM is macOS‑focused and less commonly used in this context

VirtualBox was chosen for compatibility and ease of use.

---

## Resources
- Debian Official Documentation: https://www.debian.org/doc/
- Linux Filesystem Hierarchy Standard (FHS): https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html
- SSH Manual & Security Guide: https://man.openbsd.org/ssh

---

## Use of AI
AI tools were used to:
- Clarify Linux concepts (LVM, SSH, sudo, firewall)
- Review shell scripts for correctness
- Help structure explanations for the project defense
- Improve documentation clarity (README structure and wording)

All commands, configurations, and decisions were understood, verified, and implemented manually.

---

## Final Notes
This project helped me gain a strong foundation in:
- Linux system administration
- Security best practices
- Automation using cron
- Server monitoring
- Explaining technical decisions clearly

Born2BeRoot is not just about configuration — it is about understanding why each choice matters.