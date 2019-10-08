# IoT Beacon

IoT based project for Haaga-Helia University of Applied Sciences, ICT Infrastructure Project -course. End product will be able to scan data from Bluetooth beacons with Raspberry Pi -computers and forward it to a database i.e. a server. The data will then be used to build a HTML based application which either alerts when the beacons leave a designated area or alerts when they enter a forbidden area.

# Project team

- Niko Kulmanen - Project manager
- Rasmus Ekman - Project worker
- Pekka Hämäläinen - Project worker and secretary
- Joni Mattsson - Project worker

# Installing Ubuntu server

We made a bootable Linux USB stick with Kingston 8GB flash drive using Rufus 3.8 to create an ISO-image with Xubuntu 16.04.3

- Remove LAN-cable before installation
- Open legacy boot menu with F9
- Choose USB-stick
- "Cannot boot system due to start job running for hold error"
- Try restarting again until the system starts properly

Installation steps

- English
- Install Xubuntu/Erase disk and install Xubuntu
- Continue
- Continue
- Erase disk and install Xubuntu
- Continue
- Helsinki
- Kyeboard layout Finnish and Finnish
- Your name: iotbeacon
- Computer name: rauta
- Username: iotbeacon
- Require password to login
- Continue
- Restart

Open terminal

```
Ctrl + Alt + T
```

Change keyboard layout to Finnish keyboard

```
setxkbmap fi
```

Update and reboot the system

```
sudo apt-get update
sudo apt-get upgrade
ssudo reboot
```

Firewall configuration and Apache2 Web Server installation

```
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo apt-get update
sudo apt-get install apache2
sudo reboot
```

Try localhost address on web browser, it works and opens the Apache2 default page

Find out the current IP address

```
hostname -I
```

Try 172.28.171.211 IP address on browser, this also works and opens the Apache2 default page

Enable userdir Apache module and restart the service

```
sudo a2enmod userdir
service apache2 restart
```

Go to the home directory and make the public_html folder, list the contents of the home directory to check that the public_html folder was succesfully created, and then create the index.html file inside the public_html folder

```
cd
whoami
ls
mkdir public_html
ls
cd public_html
nano index.html
```

Copy basic HTML from https://www.w3schools.com/html/tryit.asp?filename=tryhtml_basic_document and add some text

```
<!DOCTYPE html>
<html>
<body>

<h1>IoT Beacon</h1>

<p>Monialaprojekti</p>

<p>Web application will be added here<p>

</body>
</html>
```

Save the file

```
Ctrl X
Yes
Enter
```

Go to address localhost/~iotbeacon or 172.28.171.211/~iotbeacon using web browser

Both addresses work in lab environment

Edit 000-default.conf virtual host file to get a temporary domain name working

```
cd /etc/apache2/sites-available
ls
sudo nano 000-default.conf
```

```
ServerName www.iotbeacon.com
ServerAlias iotbeacon.com
```

Restart Apache service and edit hosts files inside etc folder to complete the temporary domain name configuration

```
service apache2 restart
cd /etc
sudoedit hosts
```

```
127.0.0.1 www.iotbeacon.com
127.0.0.1 iotbeacon.com
```

www.iotbeacon.com/~iotbeacon and iotbeacon.com/~iotbeacon are now working and showing the desired HTML text

### Configuring static IP address on server (currently works with Google Public DNS)

Find out the operating system version

```
cat /etc/os-release
```

Look up different instructions for installing static IP address with Xubuntu 16.04 version

https://michael.mckinnon.id.au/2016/05/05/configuring-ubuntu-16-04-static-ip-address/

https://askubuntu.com/questions/766131/how-do-i-set-a-static-ip-in-ubuntu

https://support.us.ovhcloud.com/hc/en-us/articles/360000092264-How-to-Configure-Networking-for-a-VM-Running-Ubuntu-16-04

https://www.configserverfirewall.com/ubuntu-linux/ubuntu-restart-network-interface/

https://www.youtube.com/watch?v=YkbShiPaE_0

https://www.youtube.com/watch?v=EpbtaLigCoY

https://www.howtoforge.com/tutorial/howto-set-a-static-ip-on-ubuntu/

https://www.snel.com/support/static-ip-configuration-ubuntu-16-04/

Find out the name of the used LAN interface

```
clear && echo $(ip -o -4 route get 8.8.8.8 | sed -nr 's/.*dev ([^\ ]+).*/\1/p')
ifconfig
```

Edit interfaces-file and add the required parameters for static IP address

```
sudo nano /etc/network/interfaces
```

```
auto eno1
iface eno1 inet static
address x.x.x.x
netmask x.x.x.x
gateway x.x.x.x
dns-nameservers x.x.x.x x.x.x.x
```

```
sudo ip addr flush eno1
systemctl restart networking.service
sudo reboot
```

Ultimately, I ended up changing the static IP settings graphically using Network application since it was recommended, but I couldn't get it working initially. Only after I changed the DNS addresses to Google Public DNS addresses 8.8.8.8 and 8.8.4.4, I got the internet working, so I didn't get the assigned lab environment DNS addresses working.

### Installing SSH on server

Install SSH (Secure Shell) client and server

```
sudo apt-get install -y openssh-server openssh-client
```

After installing SSH, I try to connect with an other Linux computer from the lab enviroment to the server

```
ssh iotbeacon@x.x.x.x
```

Connection is succesful

Other project member tries to connect to the server from his house using Linux terminal and SSH, connection is not succesful because apparently you can't reach these static IP addresses outside of the lab environment

### Installing Firefox on server

Establish an SSH connection in terminal using a Linux computer withing lab environment

```
- ssh iotbeacon@x.x.x.x
```

Update package lists for upgrades and new packages from repositories

```
sudo apt-get update
```

Update Firefox browser because default browser on the server does not support for example GitHub

```
sudo apt-get install firefox
```

GitHub is now supported by Firefox and writing GitHub README.md report can be done simultaneously with the server while configuring it

### Updating server from version 16.04.3 to 16.04.6

Server operating system is updated to a newer version of 16.04 LTS (Long Term Support) via graphical user interface prompt

Shut down the server before leaving school

```
sudo poweroff
```

# Bluetooth beacons

- 2 x Bluetooth low energy (BLE) BlueBeacon tags developed by BlueUp
- Beacons broadcast their identifier to nearby devices (in this project Raspberry Pi)
- Beacons are configured using BlueBeacon Manager App

### BlueBeacon Manager App

- Includes Device (beacon) informations, Global settings, Eddystone slots, iBeacon / Quuppa slots, Safety slot.
- We upgraded the firmware and changed passwords to the beacons

# Raspberry Pi 

- 3 x Raspberry Pi 3 model B

Specifications:

- 1 Gt RAM
- 1,2 GHz Broadcom BCM2837 64-bit ARMv8 Quad-core CPU
- BLE
- BCM43143 Wi-FI IEEE 802.11 b/g/n
- HDMI/RCA
- 3.5 mm 
- RJ45
- 4x USB
- MicroSD card reader

### Rasbperry Pi installation

- Heat sinks
- Case
- MicroSD card with Raspberry Pi NOOBS
- keyboard
- Mouse
- 5.1 V / 2.5 A USB power supply
- HDMI cable
- Display

### Operating system

- installed Raspbian using MicroSD card with pre-installed NOOBS (New Out Of Box Software)
- Raspbian version 10 (buster)

### Create a new sudo user

Rasbian has a default user "pi". For safety reasons we replaced pi with a new user:

```
sudo adduser xxx
sudo adduser xxx sudo
```

Add new user to same groups as "pi"

```
for GROUP in $(groups pi | sed -e 's/^pi //'); do
sudo adduser xxx $GROUP; done
```

Add nopasswd rule for new user and change "pi" to "xxx"

```
sudo cp /etc/sudoers.d/010_pi-nopasswd /etc/sudoers.d/010_xxx-nopasswd
sudo chmod u+w /etc/sudoers.d/010_xxx-nopasswd
sudo sed -i 's/pi/xxx/g' /etc/sudoers.d/010_xxx-nopasswd
sudo chmod u-w /etc/sudoers.d/010_xxx-nopasswd
sudo reboot
```

Remove user "pi"
Log in as xxx &

```
sudo deluser -remove-home pi
sudo rm -vf /etc/sudoers.d/010_pi-nopasswd
```

Changed user password & enabled ssh using Raspberry Pi Software Configuration Tool

```
sudo raspi-config
```
