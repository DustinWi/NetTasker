# NetTasker #
# Description #
This is a work in progress and this tool is not intended for anyone's use but mine.  Use of the software and instructions provided by this repository is unlicensed, unsupport, and not guaranteed.

# History #
I've become a bit drained by the high price of integration and automation software.  So, I'm making a repository to hold some stuff that I've done over the years and to maybe bring it all together in one application if I can.  I'm am not a programmer by trade, and barely one by hobby.  Any code or scripts provided will be written in my current language of choice.  My current fad is Python3.  Hope you don't mind snakes.

# To Do #
1. Figure out what the heck I actually want to do here
2. Put it all together

# Current Goal #
1. Get an Apache install running infront of a Django instance.
2. Get Django local authentication working
3. Get an API written for the Django instance.  I want everything to be API based, and leave the representation up to the client.  I will probably include a web interface of my own as well that uses the API.
4. Get an LDAP instance running
4. Get Django LDAP authentication functioning

# Dependencies #
- Apache
- Python3 (3.7.5 is what I'm currently using)
- Django (Currenlty developing on version 3.0.2)
 - Haven't decided if you need to install Djano or not.  You may be able to get by with just using what's in this repo.

# Instructions #
The instructions provided below are the bare minimum to get the system up and running.  Further tweaking and system security is up to you to figure out.

# Setup Python #

### Fedora 31 ###
1. Install it.  That's all.
'''
$ sudo dnf -y install python3 python3-pip
''' 

## Setup Django ##
1. You shouldn't need to install Django, it should all be self contained in this repository
2. Update Django to set what hosts and subnets are allowed to connect to your system.
 - Edit __NetTasker/NetTasker/settings.py__.  Find __ALLOWED_HOSTS = []__ to contain all permitted hosts and subnets.
 - Example permitting the 192.168.1.100, 127.0.0.1 (localhost) and any host on the 10.0.0.0/24 subnet:
'''
ALLOWED_HOSTS = [
 '192.168.1.100', 
 '10.0.0.\*',
 '127.0.0.1'
]
'''

## Setup Apache ##
You can use whatever web server you want, I am used to Apache so I'm using Apache.  These instructions assume you are using Apache.

### Fedora 31 ###
1. Install apache
'''
$ sudo dnf -y install httpd
'''
2. Setup Basic Apache Settings
 - As root, edit __/etc/httpd/conf/httpd.conf__
 - Search the file for the below items, and set their values as desired.
  - If you cannot find an item, add it to the bottom of the file
'''
ServerAdmin admin@example.com
ServerName  example.com
ServerTokens Prod
KeepAlive On
'''
3. Enable Apache and set the service to run on startup
'''
$ sudo systemctl start httpd
$ sudo systemctl enable httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
'''
