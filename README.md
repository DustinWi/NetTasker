# NetTasker #
# Description #
This is a work in progress and this tool is not intended for anyone's use but mine.  Use of the software and instructions provided by this repository does not grant a license, supported, or warranty.  Use at your own risk

# History #
I've become a bit drained by the high price of integration and automation software.  So, I'm making a repository to hold some stuff that I've done over the years and to maybe bring it all together in one application if I can.  I'm am not a programmer by trade, and barely one by hobby.  Any code or scripts provided will be written in my current language of choice.  My current fad is Python3.  Hope you don't mind snakes.

# To Do #
1. Figure out what the heck I actually want to do here
2. Put it all together

# Current Goal #
1. ~~Get an Apache install running infront of a Django instance.~~
2. Get Django local authentication working
3. Get an API written for the Django instance.  I want everything to be API based, and leave the representation up to the client.  I will probably include a web interface of my own as well that uses the API.
4. Get an LDAP instance running
4. Get Django LDAP authentication functioning

# Dependencies #
- Apache
- Python3 (3.7.5 is what I'm currently using)
- Django (Currenlty developing on version 3.0.2)

# Instructions #
The instructions provided below are the bare minimum to get the system up and running.  Further tweaking and system security is up to you to figure out.

# Setup Python #

### Fedora 31 ###
1. Install python3.  That's all.
```
$ sudo dnf -y install python3 python3-pip
```


## Setup Django ##
1. Install Django to the system python director
```
$ sudo pip3 install django
```

2. Update NetTasker settings.py to permit hosts and subnets to connect to your system.
 - Edit __NetTasker/NetTasker/settings.py__.  Edit __ALLOWED_HOSTS = []__ to contain all permitted hosts and subnets.
 - Example permitting the 192.168.1.100, 127.0.0.1 (localhost) and any host on the 10.0.0.0/24 subnet:
```python
ALLOWED_HOSTS = [
 '192.168.1.100', 
 '10.0.0.\*',
 '127.0.0.1'
]
```

3. Update NetTasker settings.py file to specify where your static files are located
 - This directory is where Django will store all of your static files for Apache to serve to the clients.
 - You'll need to add this variable to the bottom of the file
```python
STATIC_URL = '/path/to/static/file/location'
```

4. Populate your static cache.
```
$ cd /path/to/NetTasker/
$ python manage.py collectstatic -c -l

```

## Setup Apache ##
You can use whatever web server you want, I am used to Apache so I'm using Apache.  These instructions assume you are using Apache.

### Fedora 31 ###
1. Install apache
```
$ sudo dnf -y install httpd
```

2. Install python3-mod_wsgi to enable Apache to front-end Django
```
$ sudo dnf -y install python3-mod_wsgi
```

3. Setup Basic Apache Settings
 - As root, edit __/etc/httpd/conf/httpd.conf__
 - Search the file for the below items, and set their values as desired.
  - If you cannot find an item, add it to the bottom of the file
```
ServerAdmin admin@example.com
ServerName  example.com
ServerTokens Prod
KeepAlive On
```

4. Create a config file for the new site at __/etc/httpd/conf.d/NetTasker.conf__
	- In the below code, replace "/path/to/NetTasker/" with the path to where your NetTasker directory is
```
WSGIDaemonProcess 127.0.0.1 python-path=/path/to/NetTasker
WSGIProcessGroup 127.0.0.1

Alias /static/ /path/to/NetTasker/static/

<Directory /path/to/NetTasker/static>
 Require all granted
</Directory>

WSGIScriptAlias / /path/to/NetTasker/NetTasker/wsgi.py
<Directory /path/to/NetTasker/NetTasker>
  <Files wsgi.py>
    Require all granted
  </Files>
</Directory>


```

5. If you put your working directory in a user's home directory, you need to add the execute permissions to the user directory
```
$ chmod +x /home/YourUser/
```

6. Enable Apache and set the service to run on startup
```
$ sudo systemctl start httpd
$ sudo systemctl enable httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
```

