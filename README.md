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
5. Open gitbash, run vagrant up(It will take time and depends on Internet Connectivity)
6. Once step 5th gets completed, run vagrant ssh
7. Now rename the ssh key to LightsailKey.rsa and put that file inside the vagrant shared folder
8. Now copy the LightsailKey.rsa file to /home/vagrant/.ssh/ folder
8.1. Locate LightsailKey.rsa file first and then run pwd to know current directory
8.2. ```mv LightsailKey.rsa /home/vagrant/.ssh/```
8.3 chmod 600 ~/.ssh/LightsailKey.rsa (Changing modes)
9. Now run: ```ssh -i ~/.ssh/LightsailKey.rsa ubuntu@13.234.250.106```

### Secure server


