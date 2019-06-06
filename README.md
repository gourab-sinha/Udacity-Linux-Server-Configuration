# Linux-Server Configuration
This project is the final project to complete the Udacity Full Stack Web Development Nano Degree.
# Project Summary
The goal of this project is to host a web application either Item Catalog project and configure the linux server. It also shows how to secure the server from a number of attack vectors, install and configure a database server as well. This page will guide you how to host your web application at http://13.234.250.106 or http://ec2-13-234-250-106.ap-south-1.compute.amazonaws.com/ 
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
### Generation of Public and Private SSH Key and SSH Login setup
1. Make sure you are on vagrant machine
2. Now go inside the .ssh folder: ```cd ~/.ssh```
3. Command for gerenating: ```ssh-keygen -f ~/.ssh/grader_key.rsa```
4. Give passphrase as :```grader``` hit enter again ```grader``` hit enter and fill rest of the things.
5. Copy content of public key of grader by:```cat ~/.ssh/grader_key.rsa.pub```
6. Now login to Lightsail instance or remote host by:```ssh -i ~/.ssh/LightsailKey.rsa -p 2200 ubuntu@13.234.250.106```
7. Check whether ```/home/grader/.ssh``` folder is present or not
8. If not present then only do:```sudo mkdir /home/grader/.ssh``` 
9. Then go inside the folder and create file with name authorized_keys:```sudo nano authorized_keys```
10. Paste the content of grader_key.rsa.pub to authorized_key
11. Save:```CTRL+X``` and ```Y```
12. Now login to grader user:
    ```
    sudo -i grader
    student
    ```
13. Restricting grader files access:
    ```
    sudo chmod 700 /home/grader/.ssh
    sudo chmod 644 /home/grader/.ssh/authorized_keys
    sudo chown -R grader:grader /home/grader/.ssh
    sudo service ssh restart
    ```
13. Now you will be able to login to grader via ssh port 2200
14. Logout from grader account then ubuntu account:```exit``` and ```exit```
15. From vagrant machine terminal run: ```ssh -i `/.ssh/grader_key.rsa -p 2200 grader@13.234.250.106```
### Prepare to deploy project
*Make sure you are in grader account*
1. Configure the local timezone to UTC: 
   ```
   sudo dpkg-reconfigure tzdata
   choose: Etc or None of the above
   Select UTC
   ```
2. Install Apache:```sudo apt-get install apache2```
  * Now check with your public ip address by entering in url bar of a browser, if it loads apache's default page then it's working fine. Here public ip is 13.234.250.106
3. Run these command:
  ```
  sudo apt-get install libapache2-mod-wsgi-py3
  sudo a2enmod wsgi
  sudo service apache2 start
  ```
3. Install Git: ```sudo apt-get install git```

### Deployment of Item Catalog project
*Make sure you are in grader account*
1. Run these commands to clone and create directory:
  ```
  sudo mkdir /var/www/catalog
  cd /var/www/catalog
  sudo git clone https://github.com/gourab19964u/Udacity-Item-Catalog-Application.git catalog
  cd /var/www
  sudo chown -R grader:grader catalog/
  cd /var/www/catalog/catalog
  mv application.py __init__.py
  nano __init__.py
  ```
2. Changes in ```__init__.py``` file:
  * Find below lines
    ```
    app.debug = True
    app.run(host='0.0.0.0', port=5000, ssl_context=('cert.pem', 'key.pem'))
    ```
  * Remove above two lines and add ```app.run()```
  * Find the code for db connection or below this comment:```# Connect to Database and create database session```
    ```
    engine = create_engine('sqlite:///restaurantmenuwithusers.db', connect_args={
    'check_same_thread': False})
    ```
  * Remove above line and add: ```engine = create_engine('postgresql://catalog:catalog@localhost/catalog')```
  * Save and Exit: ```CTRL+X``` and confirm changes with ```Y```
  
3. Changes in ```database_setup.py```:
  * Find this line:```engine = create_engine('sqlite:///restaurantmenuwithusers.db')```
  * Replace the above line with:```engine = create_engine('postgresql://catalog:catalog@localhost/catalog')```
  * Save and Exit: ```CTRL+X``` and confirm changes with ```Y```
  
4. Creation of Web Server Gateway Interface:
  *Make sure you are in grader account*
  * Create wsgi file:```sudo nano /var/www/catalog/catalog.wsgi```
  * Paste the below content:
    ```
    activate_this = '/var/www/catalog/catalog/venv3/bin/activate_this.py'
    with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))

    #!/usr/bin/python3
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/catalog/")
    sys.path.insert(1, "/var/www/catalog/")

    from catalog import app as application
    application.secret_key = 'super_secret_key'
    ```
  * Save and Exit: ```CTRL+X``` and confirm changes with ```Y```
  
5. Install virtual environment:
  * Run the below command to setup virtual environment
    ```
    sudo apt-get install python3-pip
    sudo pip install virtualenv
    cd /var/www/catalog/catalog
    virtualenv -p python3 venv3
    sudo chown -R grader:grader venv3/
    ```
  * Now activate the virtual environment:``` . venv3/bin/activate```
  
    *You should install all the packages and modules for virtual environment not for grader account*
  * Install packages and modules required to run the application:
    ```
    pip3 install --upgrade pip
    pip3 install flask packaging oauth2client flask-httpauth
    pip3 install sqlalchemy flask-sqlalchemy psycopg2-binary requests
    pip3 install httplib2
    pip3 install requests
    deactivate
    ```    
6. Configure and enable a new virtual host
  * Create wsgi file: ```sudo nano /etc/apache2/mods-enabled/wsgi.conf``` 
  * Below where it says ```#WSGIPythonPath directory|directory-1:directory-2```:... add the following line: 
   ```WSGIPythonPath        /var/www/catalog/catalog/venv3/lib/python3.5/site-packages```
  * Content to be pasted in the file catalog.conf:
    ```
    sudo nano /etc/apache2/sites-available/catalog.conf
    <VirtualHost *:80>
    ServerName 13.234.250.106
    ServerAlias ec2-13-234-250-106.ap-south-1.compute.amazonaws.com
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
    	Order allow,deny
  	  Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
  	  Order allow,deny
  	  Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```
  * Save and Exit: ```CTRL+X``` and confirm changes with ```Y```
  * Run the following command: ```sudo a2ensite catalog```
  * Run the following command: ```sudo service apache2 reload```

7. Install and configure PostgreSQL:
  * Run the below commands:
  ```
  sudo apt-get install libpq-dev python-dev
  sudo apt-get install postgresql postgresql-contrib
  sudo su - postgres
  psql
  CREATE USER catalog WITH PASSWORD 'catalog';
  ALTER USER catalog CREATEDB;
  CREATE DATABASE catalog WITH OWNER catalog;
  \c catalog
  REVOKE ALL ON SCHEMA public FROM public;
  GRANT ALL ON SCHEMA public TO catalog;
  \q
  exit
  . venv3/bin/activate
  python /var/www/catalog/catalog/database_setup.py
  sudo nano /etc/postgresql/9.5/main/pg_hba.conf
  deactivate
  ```
  * Paste the following into ```pg_hba.conf``` if these are not present:
  ```
  local   all             postgres                                peer
  local   all             all                                     peer
  host    all             all             127.0.0.1/32            md5
  host    all             all             ::1/128                 md5
  ```
  * Save and Exit: ```CTRL+X``` and confirm changes with ```Y```
  * Restart apache server: ```sudo service apache2 restart```

8. Google Oauth configuration:
  * Visit your Google Developers Console.
  * Inside Create a New Project and configure its consent screen.
  * Once the project has been setup create it's OAuth Client ID under credentials.
  * Edit the credentials file.
  * Under it javascript authorized origins section add http://13.234.250.106 and http://ec2-13-234-250-106.ap-south-1.compute.amazonaws.com
  * Under its authorized redirect URIs add http://ec2-13-234-250-106.ap-south-1.compute.amazonaws.com/login/, http://ec2-13-234-250-106.ap-south-1.compute.amazonaws.com/gconnect and http://ec2-13-234-250-106.ap-south-1.compute.amazonaws.com/oauth2callback
  * Download the clients_secrets json file from here and copy its content.
  * Use sudo nano on clients_secrets.json in the catalog directory and delete its previous content. Paste the copied contents here. Save and exit.
  * In your templates folder, sudo nano into login.html and replace the previous client_ID with the new ID.
  * In ```__init__.py``` reset location for the clients_secrets file to ```/var/www/catalog/catalog/client_secrets.json```
### Final Setup and populate database:
  * Goto ```cd /var/www/catalog/catalog``` and run ```. venv/bin/activate```
  * Populate the db: ```python3 lotsofmenus.py```
  * Deactivate .venv3
  * Restart apache service: ```sudo service apache2 restart```
  * Now you can access fully working web application through these links http://13.234.250.106 or http://ec2-13-234-250-106.ap-south-1.compute.amazonaws.com/
  
### References
  * Udacity Knowledge Portal
  * Stackoverflow
  * DigitalOcean
### Troubleshoot
A. If shared folder is not visiable, run vagrant reload on gitbash terminal/cmd

B. If packages or modules are not available then 
1. sudo apt-get -qqy install python3 python3-pip
2. sudo -H pip3 install --upgrade pip
3. sudo -H pip3 install flask packaging oauth2client flask-httpauth
4. sudo -H pip3 install sqlalchemy flask-sqlalchemy psycopg2-binary bleach requests

### Note
  * Facebook OAuth won't work as Facebook only allow https sites.
  * If you are trying to login with google sign and having adress as IP address, it will show you an error as google oauth doesn't allow any ip adress in authorized redirects hence it won't work.
