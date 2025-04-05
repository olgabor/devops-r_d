# devops-r_d

## Lecture 5 - Advanced Linux

# Setup nginx

### Install nginx web server 
sudo apt update
sudo apt install -y nginx
![Screenshot Placeholder](./lecture_6/images/install_nginx.png)

###  Verify nginx web server installed 
sudo systemctl enable --now nginx
![Screenshot Placeholder](./lecture_6/images/check_nginx_installed.png)

### Install PPA repository
sudo add-apt-repository -y ppa:nginx/stable
sudo apt update
sudo apt upgrade -y
![Screenshot Placeholder](./lecture_6/images/add_ppa_repository.png)


### Check version 
nginx -v
![Screenshot Placeholder](./lecture_6/images/nginx_update.png)

### Delete ppa:repository 
sudo apt install ppa-purge
sudo ppa-purge ppa:nginx/stable 
sudo add-apt-repository --remove ppa:nginx/stable
sudo apt install --reinstall nginx nginx-common nginx-core

nginx -v

### Setup systemd Service (timer)

sudo tee /usr/local/bin/log_time.sh > /dev/null <<
```
#!/bin/bash
while true; do
    echo "$(date)" >> /var/log/times.log
    sleep 60  # Wait for 60 seconds (1 minute)
done
```

## Give access to execute 

sudo chmod +x /usr/local/bin/log_time.sh

## Add service file 

sudo tee /etc/systemd/system/log_time.service > /dev/null <<
```
[Unit]
Description=Log current time every minute
After=network.target

[Service]
ExecStart=/usr/local/bin/log_time.sh
Restart=on-failure  # Restart only if the service fails
User=root

[Install]
WantedBy=multi-user.target
```

## Copy service file to /etc/ 

sudo cp /usr/local/bin/log_time.sh.service /etc/systemd/system/log_time.service


## Create a timer 

sudo tee /etc/systemd/system/log_time.timer > /dev/null <<
```
[Unit]
Description=Run log_time.service every minute

[Timer]
OnBootSec=1min
OnUnitActiveSec=1min
Unit=log_time.service

[Install]
WantedBy=timers.target
```

## Launch the script 

sudo systemctl daemon-reload
sudo systemctl enable --now log_time.timer
sudo systemctl status log_time.service

sudo nano /usr/local/bin/log_time.sh
sudo systemctl status log_time.timer

![Screenshot Placeholder](./lecture_6/images/start_timer.png)
 
sudo systemctl stop log_time.timer

![Screenshot Placeholder](./lecture_6/images/stop_timer.png)

# Setup firewall


## Denying access to SSH from the original IP (1.2.3.4), but allowing others (5.6.7.8)

sudo iptables -A INPUT -p tcp --dport 22 -s 1.2.3.4 -j DROP
sudo iptables -A INPUT -p tcp --dport 22 -s 5.6.7.8 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4

![Screenshot Placeholder](./lecture_6/images/iptable_create_rules.png)

## Setup Fail2Ban with universe

sudo add-apt-repository universe
sudo apt update
sudo apt install -y fail2ban

## Create config 
sudo touch /etc/fail2ban/jail.local
sudo chmod +x /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```
[sshd]
enabled = true
bantime = 10m
findtime = 10m
maxretry = 3
```
## Restart fail2ban

sudo systemctl restart fail2ban
sudo fail2ban-client status sshd

![Screenshot Placeholder](./lecture_6/images/fairban.png)


# Create and mount new disk space 

## Check disks 
lsblk

![Screenshot Placeholder](./lecture_6/images/disks.png)

## Add new disk to vm machine 

![Screenshot Placeholder](./lecture_6/add_new_disk.png)

## Partition the disk 
sudo fdisk /dev/vda
n -> new space 
p -> primary 

![Screenshot Placeholder](./lecture_6/partition_disk.png)

## Format the disk
sudo mkfs.ext4 /dev/vda1
sudo mkdir /mnt/data

sudo mount /dev/vda1 /mnt/data

## Add to /etc/fstab for autoboot

### get UUID 
sudo blkid /dev/vda1
/dev/vda1: UUID="3551257c-7dca-4e9c-ae6f-a68e6c21e606" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="5501707d-01"

sudo nano /etc/fstab

![Screenshot Placeholder](./lecture_6/images/add_fstab.png)

sudo mount -a

![Screenshot Placeholder](./lecture_6/images/show_new_disk_mounted.png)
