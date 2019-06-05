# Linux-Server Configuration
This project is the final project to complete the Udacity Full Stack Web Development Nano Degree.
# Project Summary
The goal of this project is to host a web application either Item Catalog project and configure the linux server. It also shows how to secure the server from a number of attack vectors, install and configure a database server as well. This page will guide you how to do it.
## Steps  
### Amazon Lightsail Account Creation
1. Visit to [Amazon Lightsail](https://lightsail.aws.amazon.com/)
2. Sign In or Create Account
3. Create Instance of Linux Server and choose OS as Ubuntu 16.04
4. Choose the lowest to get one month free tier access for a month
5. Give intance hostname and click on create
6. Note down public IP address and download ssh key to local machine.

### Local machine setup to access lightsail via ssh
* Download

1. [Git](https://git-scm.com/download/win)
2. [Virtual Box](https://download.virtualbox.org/virtualbox/6.0.4/VirtualBox-6.0.4-128413-Win.exe)
3. [Vagrant](https://releases.hashicorp.com/vagrant/2.2.4/vagrant_2.2.4_x86_64.msi)
4. [Vagrantfile](https://s3.amazonaws.com/video.udacity-data.com/topher/2019/March/5c7ebe7a_vagrant-configuration-windows/vagrant-configuration-windows.zip)

* Installation and Setup the environment

1. Git 
2. VirtualBox
3. Vagrant
4. Create folder or use existing folder and paste Vagrantfile
5. Open gitbash, run ```vagrant up``` (It will take time and depends on Internet Connectivity)
6. Once step 5th gets completed, run vagrant ssh
7. Now rename the ssh key to LightsailKey.rsa and put that file inside the vagrant shared folder
8. Now copy the LightsailKey.rsa file to /home/vagrant/.ssh/ folder
   * Locate LightsailKey.rsa file first  
   * ```mv LightsailKey.rsa /home/vagrant/.ssh/```
   * ```chmod 600 ~/.ssh/LightsailKey.rsa``` (Changing modes)
9. Now run: ```ssh -i ~/.ssh/LightsailKey.rsa ubuntu@13.234.250.106``` here public ip is 13.234.250.106
10. Now you are on login to lightsail instance and user is ubuntu and don't change or swtich to local machine until it is mentioned

### Secure Server
1. Update all installed packages
    * ```sudo apt-get update```
    * ```sudo apt-get upgrade```
2. Change port 22 to 2200
    * ```sudo nano /etc/ssh/sshd_config```
    * Find Port 22 inside sshd_config file
    * To avoid risk just add ```Port 2200``` below Port 22 line
    * Save file by ctrl + x
    * Hit Enter to save file without changing file name
    * Restart ssh service : ```sudo service ssh restart```
    * Switch to local machine either vagrant, run ```exit``` in ubuntu terminal
    * Now try to login to lightsail instance via ssh port 2200: ```ssh -i ~/.ssh/LightsailKey.rsa -p 2200 ubuntu@13.234.250.106```
    * If it works then remove port 22 from the sshd_config file.
    * If it doesn't work then goto amazon lightsail account and select networking and add custom port 2200 as TCP
    * Restart you machine. 
    * Now try: ```ssh -i ~/.ssh/LightsailKey.rsa -p 2200 ubuntu@13.234.250.106```
    * Run the following commands:
      ```
      sudo ufw status
      sudo ufw default deny incoming
      sudo ufw default allow outgoing
      sudo ufw allow 2200/tcp
      sudo ufw allow www
      sudo ufw allow 123/udp
      sudo ufw deny 22
      sudo ufw enable
      y
      exit
      ```
    * To check the configuration type `sudo ufw status`. The output must look like this:

      ```
      To                         Action      From
      --                         ------      ----
      22                         DENY        Anywhere
      2200/tcp                   ALLOW       Anywhere
      80/tcp                     ALLOW       Anywhere
      123/udp                    ALLOW       Anywhere
      22 (v6)                    DENY        Anywhere (v6)
      2200/tcp (v6)              ALLOW       Anywhere (v6)
      80/tcp (v6)                ALLOW       Anywhere (v6)
      123/udp (v6)               ALLOW       Anywhere (v6)
      ````
    * Now again goto lightsail instance networking section and add one more port 123 as UDP. 
### Give grader access
1. Login to remote host if you have not by:```ssh -i ~/.ssh/LightsailKey.rsa -p 2200 ubuntu@13.234.250.106```
2. Create grader user account
    * Run these commands:```sudo adduser grader```
    * Give sudo access: ```sudo visudo``` under ```root ALL=(ALL:ALL) ALL``` write ```grader ALL=(ALL:ALL) ALL``` 
    * Save: CTRL+X and type Y
3. Exit from remote host: ```exit```
### Generation of Public and Private SSH Key
1. Make sure you are on vagrant machine
2. Now go inside the .ssh folder: ```cd ~/.ssh```
3. Command for gerenating: ```ssh-keygen -f ~/.ssh/grader_key.rsa```
4. Give passphrase as :```grader``` hit enter again ```grader``` hit enter and fill rest of the things.
5. Copy content of public key of grader by:```cat ~/.ssh/grader_key.rsa.pub```
