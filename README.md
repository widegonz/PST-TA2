```bash
sudo apt install ros-humble-gazebo-*
```

```bash
sudo apt install ros-humble-cartographer
sudo apt install ros-humble-cartographer-ros
```

```bash
sudo apt install ros-humble-navigation2
sudo apt install ros-humble-nav2-bringup
```

```bash
mkdir -p ~/turtlebot3_ws/src
cd ~/turtlebot3_ws/src/
git clone -b humble https://github.com/ROBOTIS-GIT/DynamixelSDK.git
git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3.git
sudo apt install python3-colcon-common-extensions
cd ~/turtlebot3_ws
colcon build --symlink-install
echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
source ~/.bashrc
```
```bash
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
echo 'source /usr/share/gazebo/setup.sh' >> ~/.bashrc
source ~/.bashrc
```
```bash
sudo apt install rpi-imager
rpi-imager
```

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: yes
      dhcp6: yes
      optional: true
  wifis:
    wlan0:
      dhcp4: yes
      dhcp6: yes
      access-points:
        WIFI_SSID:
          password: WIFI_PASSWORD
```

```bash
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

```bash
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Unattended-Upgrade "0";
```

```bash
systemctl mask systemd-networkd-wait-online.service
```

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```
```bash
reboot
```

```bash
sudo nano /etc/ssh/sshd_config.d/50-cloud-init.conf
```
```bash
PasswordAuthentication yes
```

```bash
reboot
sudo apt update
sudo apt install net-tools
ifconfig
```

```bash
ssh ubuntu@{IP Address of Raspberry PI}
```

```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

```bash
free -h
```
