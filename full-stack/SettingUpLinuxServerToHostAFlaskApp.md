# Setting up a Linux Server to Host a Flask App

1. Set up ssh.
1. [Install LAMP (Linux, Apache, MySQL(or PostgreSQL), Python)](http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html)
1. [Inital Server Setup with Ubuntu (12.04)](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-12-04)
1. Create authorized keys at /home/#USER/.ssh
1. Change owner and give permission to new user to access public key for ssh:
	1. chown grader:grader /home/grader/.ssh
	1. chmod 700 /home/grader/.ssh
	1. chown grader:grader /home/grader/.ssh/authorized_keys
	1. chmod 600 /home/grader/.ssh/authorized_keys
1. Login as new user. E.g. ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@35.166.190.110 - in this case ssh port has changed to 2200 per step in Initial Server Setup with Ubuntu.
1. Configure firewall settings per Configuring Linux Web Server notes.
1. [Deploy a Flask App on Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
1. Install Git
	`sudo apt-get install git-all`
1. Accessing site through http://ec2-XX-XX-XXX-XXX.us-west-2.compute.amazonaws.com/
http://ec2-35-166-190-110.us-west-2.compute.amazonaws.com/
1. [Secure PostgreSQL on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
1. 