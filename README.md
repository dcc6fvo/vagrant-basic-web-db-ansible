vagrant-basic-web-db-ansible
==========

This is a simple vagrant configuration that provides 03 virtual machines: one with nginx and php, another with MySQL database and finally, the management machine with ansible software. Don't forget to create/use another SSH private key.

There is aa puppet + vagrant configuration version with the "puppet" branch.

Prerequisites
-----------------------
Vagrant and virtualbox are both essential and can be easily installed on ubuntu/debian like distro:

	sudo apt-get install virtualbox && sudo apt-get install vagrant


Installation && Running
-----------------------

Do a git clone of the project:

	git clone https://github.com/dcc6fvo/vagrant-basico-web-db.git

Access the newly created folder with the git clone command and type the following command:

	vagrant up

To forcible destroy all the configurations:

	vagrant detroy -f
