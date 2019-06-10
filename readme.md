# Linux-Server-Configuration
Udacity Full Stack

## Server Information
This server is run on the Amazon LightSail platform [Lightsail](https://lightsail.aws.amazon.com)
* The ip address is 18.221.242.20 or 18.221.242.20:80
* ssh port 2200
* To access this server please go to http://34.229.86.248

## Installed Packages
**using $ sudo apt-get install <package name>**

* postgresql
* postgresql-contrib
* postgresql-server-dev-9.5
* python2.7
* python-flask
* python-pip 
* python-oauth2client
* apache2
* libapache2-mod-wsgi
* httplib2

**using $ pip install <package name> or $ sudo -H pip install <package name> as required**

- psycopg2
- psycopg2-binary
- flask
- requests
- SQLAlchemy
- google-api-python-client oauth2client

**Other Software**

- PuTTY 0.71 - [Get PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Git Bash - [Get Git Bash](https://git-scm.com/downloads)
- Notepad++ - [Get Notepad++](https://notepad-plus-plus.org/)
- Notepad++ Markdown viewer- [Homepage found here](https://nea.github.io/MarkdownViewerPlusPlus/)

## Configurations

### Amazon Lightsail Server Acquisition
* Amazon Lightsail create an AWS account https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/
* Create Instance, Select platform (linux) and blueprint OS only (ubuntu 16.04 LTS).
* Select the option for a static ip address.
* Download the default private key (.pem file) to be used with PuTTY or Git Bash to access the server via ssh. This key will be used by the ubuntu user.
* Follow the Lightsail instructions on configuring SSH access with putty. The web-based terminal times out too often. Configure PuTTY for ssh port 22 and also 2200 but verify access only for 22. We will delete 22 later and switch to 2200.
* Select the Networking tab and add ports 2200, 123 as TCP/UDP, respectively.  Leave the other ports for now or you'll lose the ability to ssh and view pages.

### Secure the Server
* Update installed packages with `$ sudo apt-get update`
* Now perform an update by typing `$ sudo apt-get update`
* Upgrade the existing distribution by typing `$ sudo apt-get upgrade`
* Finally, clean up old packages with `$ sudo apt-get autoremove`
  
* Changing the SSH configuration on the lightsail server

  * `$ sudo nano /etc/ssh/sshd_config`

  * Change *'Port'* from 22 to 2200:
    ```
    # What ports, IPs and protocols we listen for
    Port 2200
    ```

  - Disable root login setting the *'PermitRootLogin'* flag to no

    ```
    # Authentication:
    LoginGraceTime 120
    PermitRootLogin no
    StrictModes yes
    ```
  - Enforce key-based authentication by updating *'PasswordAuthentication'* and setting to no

    ```
    # Disable tunneled cleared passwords by setting PasswordAuthentication to no
    PasswordAuthentication no
    ```
  - Save your changes with `<ctrl> x` 

  - Go back and verify that you have added port 2200 in the lightsail firewall.  If you have not added it, go back and do it now.  

  - If you are certain that it has been added, type the next command 

  - `$ sudo service sshd restart` This will restart the ssh server

* Configure the Ubuntu Uncomplicated Firewall (UFW) https://help.ubuntu.com/community/UFW

  * Verify that UFW is not running  `$ sudo ufw status` , stop UFW if it is running `$ sudo ufw disable`
  * Disable all incoming ports `$ sudo ufw default deny incoming`
  * Allow all outgoing ports `$ sudo ufw default allow outgoing`
  * Enable the following ports
    * `$ sudo ufw allow 80/tcp`
    * `$ sudo ufw allow 2200/tcp`
    * `$ sudo ufw allow 123/udp`
  * Verify UFW that your ports are added by typing `$ sudo ufw status`
  * Enable UFW by typing `$ sudo ufw enable`
  * View the present UFW status by typing `$ sudo ufw status`

  ```
  ubuntu@ip-172-26-11-45:/var/www/catalog/catalog$ sudo ufw status
  Status: active
  
  To                         Action      From
  ------
  2200/tcp                   ALLOW       Anywhere
  80/tcp                     ALLOW       Anywhere
  123/udp                    ALLOW       Anywhere
  2200/tcp (v6)              ALLOW       Anywhere (v6)
  80/tcp (v6)                ALLOW       Anywhere (v6)
  123/udp (v6)               ALLOW       Anywhere (v6)
  
  ubuntu@ip-172-26-11-45:/var/www/catalog/catalog$
  ```

* Now go back to putty and try connecting to port 2200.  It should work now.

* Next, go back to the LightSail firewall and remove port 22.

### Adding a grader user
* Add a new user named *'grader'*  `$ sudo adduser grader` 

  * answer the questions 

* Grant the new user sudo privileges  [add user and grant sudo](<https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04>)

* Generate the RSA key-pair from your Windows PC using Git Bash.  If you have MacOS/*nix you can use the command prompt:

  * `user~ $ ssh-keygen` 

    ```
    user ~
    $ ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/c/Users/user/.ssh/id_rsa):
    ```

    ```
    user ~
    $ cd .ssh
    user .ssh
    $ ls 
    id_rsa.pub  id_rsa
    ```

  * copy the contents of *'id_rsa.pub'* to the clipboard and exit Git Bash

* Now, with Putty, ssh into your server and use the following steps to enable the grader account with the RSA key you just created:

  * CD into the grader's home directory  `$ cd /home/grader`

  * Create a directory named .ssh  `$ mkdir .ssh`

  * Create and edit the file to store the RSA key   `$ nano .ssh/authorized_keys`

  * Copy the RSA key in the clipboard into the file created by nano and save it.

  * Change permissions on the directory and file.

    * `$ sudo chmod 700 /home/grader/.ssh`
    * `$ sudo chmod 644 /home/grader/.ssh/authorized_keys`

  * Change the owner from root to grader.

    * `sudo chown -R grader:grader /home/grader/.ssh`

  * Restart the ssh service   `$ sudo service ssh restart`

  * Open a Git Bash terminal window  and access the server as grader

    * `$  ssh grader@34.229.86.248 -p 2200 -i ~/id_rsa `

    ```
    user ~
    $  ssh grader@34.229.86.248 -p 2200 -i ~/id_rsa
    Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-1052-aws x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud
    
    5 packages can be updated.
    0 updates are security updates.
    
    New release '18.04.2 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
    
    Last login: Mon Jun 10 15:04:33 2019 from 108.72.243.62
    grader@ip-172-26-11-45:~$
    ```

    

## Prepare Sever for the Application

- Set Time Zone to UTC

  - use this command   `$ date`   to view current server date/time/time zone

    ```
    ubuntu@ip-172-26-6-239:/$ date
    Sat Apr 20 22:46:47 UTC 2019
    ubuntu@ip-172-26-6-239:/$
    ```

  - If it needs to be changed   `sudo dpkg-reconfigure tzdata`

- Installed required packages listed in the ***Installed Packages*** section above. 

- Enable mod_wsgi  `$ sudo a2enmod wsgi`

- Start the web server `$ sudo service apache2 start`  or  `$ sudo service apache2 restart`

- Enter the public IP address from Lightsail into a browser and the apache2 default page should be displayed. Do not proceed if it does not display, fix the issues.

- Configure PostgreSQL

  - Create the role postgres and password   `$ sudo -u postgres psql postgres`

  - password `\password postgres`

  - Exit the postgreSQL prompt  `\q` or `<ctrl>D`

  - Since the postgres user is a super user we need to create a user with limited access to our database.  `$ sudo -i -u postgres`

  - Create a user named catalog  `createusercatalog -P --interactive`

    ```
    postgres@ip-172-26-6-239:~$ createuser catalog -P --interactive
    Enter password for new role: xxxxxxxx
    Enter it again: xxxxxxxx
    Shall the new role be a superuser? (y/n) n
    Shall the new role be allowed to create databases? (y/n) y
    Shall the new role be allowed to create more new roles? (y/n) y
    postgres@ip-172-26-6-239:~$
    ```

  - Exit with `<ctrl>D`

  - Create the vg database  `sudo -u postgres createdb -O catalog itemcatalog

  - Verify the creation with `sudo -u postgres psql itemcatalog` and then `\l`

    ```
                                       List of databases
        Name     |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges
    -------------+----------+----------+-------------+-------------+-----------------------
     vgcatalog | catalog  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
     postgres    | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
     template0   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                 |          |          |             |             | postgres=CTc/postgres
     template1   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                 |          |          |             |             | postgres=CTc/postgres
    (4 rows)
    ```

  - Use `q` to quit and `<ctrl>D` to exit the postgres prompt

  - Verify that remote connections are disabled 

    - `$ sudo cat /etc/postgresql/9.5/main/pg_hba.conf`

  ```
  # PostgreSQL Client Authentication Configuration File
  # ===================================================
  #
  # Refer to the "Client Authentication" section in the PostgreSQL
  # documentation for a complete description of this file.  A short
  # synopsis follows.
  #
  # This file controls: which hosts are allowed to connect, how clients
  # are authenticated, which PostgreSQL user names they can use, which
  # databases they can access.  Records take one of these forms:
  #
  # local      DATABASE  USER  METHOD  [OPTIONS]
  # host       DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
  # hostssl    DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
  # hostnossl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
  #
  
  # Database administrative login by Unix domain socket
  local   all             postgres                                peer
  
  # TYPE  DATABASE        USER            ADDRESS                 METHOD
  
  # "local" is for Unix domain socket connections only
  local   all             all                                     peer
  # IPv4 local connections:
  host    all             all             127.0.0.1/32            md5
  # IPv6 local connections:
  host    all             all             ::1/128                 md5
  # Allow replication connections from localhost, by a user with the
  # replication privilege.
  #local   replication     postgres                                peer
  #host    replication     postgres        127.0.0.1/32            md5
  #host    replication     postgres        ::1/128                 md5
  
  ```

  

## Deploy the Catalog Project to the Server

- Create the catalog project directory  `sudo mkdir /var/www/catalog`

- CD into the directory  `sudo cd /var/www/catalog`

- Pull the application down from GIT `sudo git clone <yourlink>`

  - You should now see the following folders /var/www/catalog/catalog/

- Build the vgcatalog database by running database.py

  - `$ cd /var/www/catalog/catalog  `
  - `$ python database_setup.py`

- Next you need to populate the DB tables

  - `$ python loaddatabasecontent.py `

- Verify that the DB tables were created 

  ```
  $ sudo -u postgres psql itemcatalog
  vgcatalog=# \dt
  ```


- Create the wsgi file for the catalog program:

  - `$ sudo nano /var/www/catalog/catalog.wsgi`

  - Paste the following lines into the window(right-click):

    ```
    #!/usr/bin/python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/catalog/catalog/")
    
    from catalog import app as application
    application.secret_key = 'Add your secret key'
    ```
- Please note that the secret key is the secret key in the oauth window from google

- Create the apache2 config file to serve the catalog web site

  - `$ sudo nano /etc/apache2/sites-available/catalog.conf`

  ```
  <VirtualHost *:80>
                  ServerName 34.229.86.248
                  ServerAdmin grader@34.229.86.248
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

  
- Make sure that you change the path to match your deployment path:
	-At present the path should be '/var/www/catalog/catalog/'
	-This may change.  If you receive a 500 error check this first

- Enable catalog.conf by typing `$ sudo a2ensite catalog.conf`

- Restart apache by typing  `$ sudo service apache2 restart`

- You may be asked to authenticate.  Use the 'grader' account and password

- Enter the public IP address from Lightsail into a browser and the catalog web page should be rendered

  - If the page does not render check the error.log for errors such as missing packages, load them and restart the server.
  - `$ cat /var/log/apache2/error.log`

## Various Commands

* edit ssh settings and ports to listen on: $ sudo nano /etc/ssh/sshd_config

* restart the ssh service: $ sudo service sshd restart


* check firewall status and allowed ports: $ sudo ufw status

* Allow ports: $ sudo ufw allow <port>/<optional: protocol>

* Deny ports : $ sudo ufw deny <port>/<optional: protocol>

* Reload apache after installing software: $ sudo service apache2 reload

* Check the error log for issues to find bad configuration or missing packages: $ cd /var/log/apache2  cat error.log


## Acknowledgements

* DigitalOcean:  https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
* DigitalOcean: <https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04>
* Ubuntu help on UFW: https://help.ubuntu.com/community/UFW
* Stack Overflow
* Superuser.com
* Amazon Lightsail
* Udacity Full Stack lessons
* Udacity Knowledge Center
* Udacity Student Hub
