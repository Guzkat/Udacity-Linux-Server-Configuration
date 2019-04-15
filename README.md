# Udacity-Linux-Server-Configuration

IP address: 3.89.229.143

SSH Port 2200

URL http://3.89.229.143

# Create an instance in AWS Lightsail

Create an AWS account

Click Create instance button on the home page

Select Linux/Unix 

Select OS Only and Ubuntu 

Name your instance

Click Create button

# Setup SSH Key

Select Account page

Select Download

Rename file Lightsail_key.pem

Run on your local terminal chmod 400 Lightsail_key.pem to restrict permission

Run on your local terminal ssh ubuntu@3.89.229.143 -i ~/.ssh.Lightsail_key.pem

# Update server

Run sudo apt-get update

Run sudo apt-get upgrade

# Change SSH port 22 to 2200

Run sudo nano /etc/ssh/sshd_config

Change port #22 to port 2200 and save ctrl X

Restart SSH: sudo service ssh restart

# Configure Amazon Lightsail Firewall

Amazon Lightsail select Networking tab

Select Add another 

select custom TCP 2200

select cutom UDP 123

select save

# Configure Firewall on server

sudo ufw status

sudo ufw default deny incoming

sudo ufw default deny outgoing

sudo ufw allow 2200/tcp

sudo ufw allow 80/tcp

sudo allow 123/udp

sudo ufw enable 

sudo ufw status

# Create new user grader

Run sudo adduser grader

Run sudo visudo 

Add grader ALL=(ALL:ALL) ALL after root ALL=(ALL:ALL). Save and exit

# Create SSH key pair

On local machine run ssh grader@3.89.229.143 -p 2200 

Enter password

On server Run mkdir .ssh

Run sudo nano .ssh/authorized_keys

Copy and paste generated SSH key from local machine save and exit

Reload service sudo service ssh restart

Run sudo nano /etc/ssh/sshd_config

Change PermitRootLogin to no save and exit

# Configure local time zone

Run sudo dpkg-reconfigure tzdata

Select None of the above

Select timezone UTC

# Install Apache

Run sudo apt-get install apache2

Run sudo apt-get install python-setuptools libapache2-mod-wsgi

Run sudo service apache2 start

# Configure Apache

Create file sudo nano /etc/apache2/sites-available/catalog.conf

Add following code:

VirtualHost *:80>
    ServerName 3.89.229.143
    
    ServerAdmin admin@3.89.229.143
    
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

Save and exit

Run sudo a2ensite catalog

# Install and configure PostgressSQL

Run sudo apt-get install PostgreSQL

Login to postgress sudo su - postgres 

run sudo psql

Create new user: CREATE USER catalog WITH PASSWORD 'password'

Allow user to create DB: Database ALTER USER catalog CREATEDB 

Create DB: CREATE DATABASE catalog WITH OWNER catalog 

Connect to catalog DB: \c catalog

Revoke all rights: REVOKE ALL ON SCHEMA public FROM public 

Grant access form public to catalog: GRANT ALL ON SCHEMA public TO catalog 

Exit psql: \q 

Exit user: exit

# Clone project

Run cd /var/www/

Then make a dir 

Run sudo mkdir catalog

Change to grader sudo chown -R grader:grader catalog

Run chmod catalog

Switch to catalog directory

Run cd catalog and sudo git clone https://github.com/Guzkat/Item-catalog.git

Change file project.py to init.py: sudo mv project.py init.py

Run sudo nano init.py

Replace line app.run(host='0.0.0.0', port=8000) to app.run() in init.py file

Run sudo nano database_setup.py

Replace line engine = create_engine("sqlite:///catalog.db") to engine = create_engine('postgresql://catalog:PASSWORD@localhost/catalog')

Run sudo nano tvguide.py

Replace line engine = create_engine("sqlite:///catalog.db") to engine = create_engine('postgresql://catalog:PASSWORD@localhost/catalog') 

Run sudo python database_setup.py

Run sudo python tvguide.py

Restart Apache: sudo service apache2 reload

# Add wsgi file

Run sudo nano /var/www/catalog/catalog.wsgi

Add following code:

#!/usr/bin/python
import sys
import logging
import os
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/catalog")

from init import app as application
application.secret_key = 'super_secret_key'

save and exit

Restart Apache: sudo service apache2 reload

# Install virtual environment and flask framework

Run pip sudo apt-get install python-pip

Run sudo apt-get install python-virtualenv

Run source venv/bin/activate

Change permission sudo chmod -777 venv

Install flask: pip install Flask

Run pip install bleach httplib2 request oauth2client sqlalchemy python-psycopg2.

Install packages:

Run:
   sudo pip install httplib2
   sudo pip install requests
   sudo pip install --upgrade oauth2client
   sudo pip install sqlalchemy
   sudo pip install flask
   sudo apt-get install libpq-dev
   sudo pip install psycopg2

Restart Apache: sudo service apache2 reload 

# Run application

Follow link http://3.89.229.143 in your browser to run application. 

# Sources

Amazon Lighrsail: https://lightsail.aws.amazon.com/ls/webapp/home/instances

Udacity: https://classroom.udacity.com/nanodegrees/nd004/parts/b2de4bd4-ef07-45b1-9f49-0e51e8f1336e

PosgreSql Docs: https://www.postgresql.org/docs/9.5/static/index.html

Flask Docs: http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/

Stackoverflow

Github
