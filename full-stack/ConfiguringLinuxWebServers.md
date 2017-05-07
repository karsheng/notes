# Configuring Linux Web Servers

## Intro to Linux
1. Linux is a free operating system. Free as in Beer, means you don't have to pay for it, just like when someone buys you a beer. Free as in speech refers to your rights or liberties with the software.
1. [Redhat](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) - For entreprise. Not free.
1. [Ubuntu](https://www.ubuntu.com/) - Has many versions. Ease of use on servers, desktops, laptops.
1. Debian - stable and reliable. Slow updates cycle.
1. [Centos](https://www.centos.org/)
1. [CoreOs](https://coreos.com/) - Clustered, containerized deployment of apps
1. [Linux Mint](https://www.linuxmint.com/) - Desktop users with proprietary media support
1. Type `vagrant status`
This command will show you the current status of the virtual machine. It should currently read “default running (virtualbox)” along with some other information.
1. Type `vagrant suspend`
This command suspends your virtual machine. All of your work is saved and the machine is put into a “sleep mode” of sorts. The machines state is saved and it’s very quick to stop and start your work. You should use this command if you plan to just take a short break from your work but don’t want to leave the virtual machine running.
1. Type `vagrant up`
This gets your virtual machine up and running again. Notice we didn’t have to redownload the virtual machine image, since it’s already been downloaded.
1. Type `vagrant ssh`
This command will actually connect to and log you into your virtual machine. Once done you will see a few lines of text showing various performance statistics of the virtual machine along with a new command line prompt that reads vagrant@vagrant-ubuntu-trusty-64:~$
1. `vagrant halt`
This command halts your virtual machine. All of your work is saved and the machine is turned off - think of this as “turning the power off”. It’s much slower to stop and start your virtual machine using this command, but it does free up all of your RAM once the machine has been stopped. You should use this command if you plan to take an extended break from your work, like when you are done for the day. The command vagrant up will turn your machine back on and you can continue your work.
1. `vagrant destroy`
This command destroys your virtual machine. Your work is not saved, the machine is turned off and forgotten about for the most part. Think of this as formatting the hard drive of a computer. You can always use vagrant up to relaunch the machine but you’ll be left with the baseline Linux installation from the beginning of this course. You should not have to use this command at any time during this course unless, at some point in time, you perform a task on the virtual machine that makes it completely inoperable.
1. Most likely, you will be given a virtual machine from a service provider like Amazon, and you're log in to it via SSH. This experience is pretty much the same as what you have configured now on your computer with vagrant.
1. [Bash Startup Files](http://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)
1. `cd /` to go to the root and `ls -al` to see all files (including hidden ones) in long format.
1. `etc` is where configuration files live - to be modified when setting up server.
1. `var` is variable files - files that you expect to grow or change in size overtime. We typically find system and application logs within this directory.
1. `bin` is where executable binaries are stored that are accessible by all users. These are applications you run like the `ls` command.
1. `sbin` is very similar to `bin` - binaries only be used by the root user for system administration and maintenance purposes.
1. `lib` is the libraries that support the binaries that are located around the system.
1. `usr` is for user programs. Binaries within `bin` are required for a boot-up and system maintenance processes, and the binaries in `usr` necessarily aren't required for that.
1. `echo $PATH` list down the directories Linux traverses through to look up for the binaries. The `$` is the shortcut here such that you just need to type `ls` rather than `/bin ls` every time.

## Linux Sercurity
1. Rule of least priviledge - a user or an app only has enough permission to do its job, nothing extra.
1. `ls -al /home/ubuntu/.ssh` is an example command that only root users are allowed to run.
1. It's common to disable the ability to remotely l og in as root. Instead, we'll log in as a user we create, and then we can run individual commands as root by using another command. This is to make a potential attacker job a little more difficult.
1. To run the command as super user `sudo ls -al /home/ubuntu/.ssh`
1. It's typically regarded as best practice not to use the `su` command. This is to prevent you forgetting that you're in `su` and do some permanent damage to your system.
1. Package source list - list of software for Linux.
1. All available package sources are listed in `/etc/apt/sources.list`.
1. `trusty` - code name for the version of Ubuntu. Ubuntu is based off Debian - hence `deb` in the beginning.
1. `sudo apt-get update` to  package source list. Have to run as root user. This doesn't perform any changes to the software on the system. It just make sure your system is aware of the latest information stored within all of these repositories that you're making use of. After this do `sudo apt-get upgrade` to install.
1. `sudo apt-get autoremove` to remove stuff no longer required.
1. `sudo apt-get install` to install stuff. E.g. `sudo apt-get install finger`
1. [To finda out packages of Ubuntu](http://packages.ubuntu.com)
1. [List of sections in "trusty"](http://packages.ubuntu.com/trusty/)
1. `finger` looks up various info about a user and display it in an easy to read format. E.g. `finger vagrant`
1. `finger` retrieves info from `/etc/passwd`. Each line displayed is an entry for a single user, and each entry has a number of fields that are separated by colon characters.
1. username:password:UID:GID:UID info:home directory:command/shell
E.g. `vagrant:x:1000:1000::/home/vagrant:/bin/bas`username: the user’s login name
	1. password: the password, will simply be an x if it’s encrypted
	1. user ID (UID): the user’s ID number in the system. 0 is root, 1-99 are for predefined users, and 100-999 are for other system accounts
	1. group ID (GID): Primary group ID, stored in /etc/group.
	1. user ID info: Metadata about the user; phone, email, name, etc.
	1. home directory: Where the user is sent upon login. Generally /home/
	1. command/shell: The absolute path of a command or shell (usually /bin/bash). Does not have to be a shell though!
1. Root user will always have UID and GID of 0 as default by Linux.
1. `sudo adduser` to create user. Usage: `sudo adduser student`.
1. `vagrant ssh` is basically shortcut for `ssh vagrant@127.0.0.1 -p 2222`
	1. SSH is the application we use to remotely connect to the server.
	1. 127.0.0.1 is the IP address to connect to. In this case it's the standard for localhost or the same computer you're currently on.
	1. -p 2222 specifies the port to be connected.
1. `sudo cat /etc/sudoers` to read the list of super users.
1. `#includedir /etc/sudoers.d` tells the system to also look through any files in etc/sudoers.d and include those as if they were written directly within this file. This is a common pattern since distribution updates could overwrite this file. And if that were to happen, you would lose all of the users you added. By keeping your customizations in this other directory, the system eliminates that risk.
1. `sudo ls /etc/sudoers.d` prints all the files within the folder.
1. To give sudo access to a user e.g. student:
```
sudo cp /etc/sudoers.d/vagrant /etc/sudoers.d/student # this copies the existing user with sudo access to the student folder
sudo nano /etc/sudoers.d/student #this opens nano editor to edit the file.
# Once inside change the name vagrant to student
```
1. [More info on Ubuntu sudoers.](https://help.ubuntu.com/community/Sudoers)
1. `sudo passwd -e student` forces `student` to change password in the next login.
1. Key based authentication - relies on physical files located on the server and your personal machine, the one you're logging in from.
1. [Public key and private key explaination](https://www.youtube.com/watch?v=KuCt1GhzKJE)
1. Generate key pair on local machine, not server. 
1. Generate key pair using application called ssh-keygen.
1. Give a file name to the key pair e.g. `Users/Udacity/.ssh/linuxCourse` - keep this default.
1. A passphrase to be given just in case someone does happen to get these files the passphrase will prevent them from using them.
1. [Generating key pairs](https://www.youtube.com/watch?v=afOCp8d60U8)
1. `ssh-keygen` support DSA, ECDSA, RSA, ED25519 and RSA key types with SSH protocol version 2. SHA256 and MD5 are hashing algorithms that are not suitable for public key encryption.
1. [Installing a Public Key](https://www.youtube.com/watch?v=k5JTVI6c7w8)
1. With the public key created, use `ssh student@127.0.0.1 -p 2222 -i ~/.ssh/linuxCourse` use this to log in without having to enter your remote password for this user.
1. Disable password based login forces users to only be able to login using a key pair. Edit `sudo nano /etc/ssh/sshd_config`. Change `passwordAuthentication`to `no`.
1. Restart the service using `sudo service ssh restart`
1. File permissions:
	e.g. `-rw-r--r--`
	1. `-` indicates it's a file. Otherwise it is `d` which means directory
	1. `rw-` Owner - has the right to read and write but cannot execute (if they could there will be an `x` at the end)
	1. `r--` Group - has the right to read only, cannot write and execute
	1. `r--` Everyone - has the right to read only, cannot write and execute
1. `drwxr-xr-x@  8 karshenglee  staff     272 Aug 10 20:41 ud989-pizzamvo`
	1. `karshenglee` is the owner
	1. `staff` is the gorup
1. Octal permissions:
	1. r = 4
	1. w = 2
	1. x = 1
	1. no permission = 0
	1. e.g. `rw- r-- r--` 644
1. `chown` - change owner
1. `chgrp` - change group
1. Each applications are configured to respond to requests destined for a specific port. For example, a web server would by default respond on port 80, the default port for HTTP. We can control which ports our server is allowed to accept requests for using firewall.
1. Default ports for popular services:
	1. HTTP: 80
	1. SSH: 22
	1. POP3: 110
	1. HTTPS: 443
	1. FTP: 21
	1. SMTP: 25
1. Ubuntu default firewall:
	1. `sudo ufw status` - check status active/inactive
	1. `sudo ufw default deny incoming` - block all incoming requests initially, good practice for easier management of rules
	1. `sudo ufw default allow outgoing` - allows all outgoing connections i.e. any requests our server is trying to send to the internet.
	1. `sudo ufw allow ssh` - allows ssh connection to server.
	1. `sudo ufw allow 2222/tcp` - as vagrant set up our ssh on port 2222, we also need to allow it.
	1. `sudo ufw allow www` - support basic HTTP server
	1. `sudo ufw enable` - enables firewall
1. Helpful links:
	1. ["LAMP" Stack (Linux, Apache, MySQL, PHP)](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-14-04)
	1. ["LEMP" Stack (Linux, nginx, MySQL, PHP)](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-14-04)
	1. [PEPS Mail and File Storage](https://www.digitalocean.com/community/tutorials/how-to-run-your-own-mail-server-and-file-storage-with-peps-on-ubuntu-14-04)
	1. [Mail-in-a-Box Email Server](https://www.digitalocean.com/community/tutorials/how-to-run-your-own-mail-server-with-mail-in-a-box-on-ubuntu-14-04)
	1. [Lita IRC Chat Bot](https://www.digitalocean.com/community/tutorials/how-to-install-the-lita-chat-bot-for-irc-on-ubuntu-14-04)
1. 