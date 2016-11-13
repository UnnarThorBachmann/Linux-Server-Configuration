November 2016

# Linux-Server-Configuration

This is a project done by Unnar Thor Bachmann in the Udacity's full-stack-developer course.

## How to gain access to the server.
* Clone this repository.
* Open the folder in a unix shell.
* Type `ssh -p 2200 -i .ssh/udacity_key grader@35.162.181.90`
* As passphrase type `1R7p9`.

## How to run the webpage
* Open the url `http://ec2-35-162-181-90.us-west-2.compute.amazonaws.com/`
* Or `http://35.162.181.90/` (ip address 35.162.181.90).

## Third Party Resources

* [How to deplay a flask application](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps).
* [How to change timezone on Ubuntu](http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt).
* [How to set up postgresql on ubuntu](https://help.ubuntu.com/community/PostgreSQL).
* [An Udacity Forum on modules needed to run the Flask application](https://discussions.udacity.com/t/importerror-no-module-named-psycopg2-project5/35018/5)
* [The Flask webpage](http://flask.pocoo.org/)

## Software installed

	* Ubuntu packages (`sudo apt-get install`):
		* `apache2`
		* `libapache2-mod-wsgi python-dev`
		* `postgresql-client``
		* `postgresql postgresql-contrib`
		* `git`
		* `python-pip`

	* Python modules (`sudo pip install`)
		* `httplib2`
    	* `Flask `
    	* `python-psycopg2`
    	* `sqlalchemy`
    	* `Flask-SQLAlchemy`
    	* `--upgrade oauth2client`


## Configurations made

1. Launched my Virtual machine from my github shell on my local machine.

2. Used  `ssh -i .ssh/udacity_key root@35.162.181.90` to enter server as root.

3. Created a new user
	* `sudo adduser grader`
	* Password selected as `G8rA9L7`
	* sudo vim /etc/ssh/sshd_config
	* Changed the line `PasswordAuthentication no` to `PasswordAuthentication yes` in order to be able to access the server as grader temporarily.
	* Restarted ssh with `sudo service ssh restart`.

4. Giving `grader` the permission to sudo upon entering password.
	* `vim /etc/sudoers.d/grader`
	* Adding to the file: `grader All=(ALL) PASSWD:ALL`

5. Updating all currently installed packages
	* `ssh -p 22 grader@35.162.181.90`
	* `sudo apt-get update`
	*`sudo apt-get upgrade`

6. Change the `ssh` port from 22 to 2200
	* Changed the file `/etc/ssh/sshd_config` as sudo. The lines changed were `Port 22` to `Port 2200`
	* Restarted ssh with `sudo service ssh restart`.

7. Enforced key-based `ssh` authentication
	* On a local virtual machine applied `ssh_keygen` to generate private and public key. Selected `1R7p9` as a passprase. Next entered server as grader (`ssh -p 2200 grader@35.162.181.90`). 
	* `mkdir ~/.ssh`
	* `touch .ssh/authorized_key`
	* Exited the server with `exit`
	* Entered as `root`. Copied the public key to `~/.ssh/authorized_key`
	* Copied the key to the grader: `cp .ssh/authorized_key /home/grader/.ssh/authorized_key`
	* Changed the line `PasswordAuthentication yes` to `PasswordAuthentication no`.
	* Restarted `ssh` with `sudo service ssh restart`.
	* Disabled root login by deleting the `/root/.ssh` directory. 

	After these steps it the only possible way to log in with ssh is with the command `ssh -p 2200 -i .ssh/udacity_key grader@35.162.181.90` and the passprase `1R7p9`

8. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
	* `sudo ufw default deny incoming`
	* `sudo ufw default allow outgoing`
	* `sudo ufw allow 2200`
	* `sudo ufw allow www`
	* `sudo ufw allow ntp`

9. Configure the local timezone to UTC
	*As sudo wrote `sudo dpkg-reconfigure tzdata` in the - selecting the option `None of the above` and then `UTC`. [See](http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt).

10. Install and configure Apache to serve a Python mod_wsgi application
	* Logged in as `grader`. 
	* `sudo apt-get install apache2`
	* The apache server can now be reache by entering `http://35.162.181.90:80` or `http://ec2-35-162-181-90.us-west-2.compute.amazonaws.com/` in a browser.
	* Configured the Apache server to handle WSGI requests: 
		*`sudo apt-get install libapache2-mod-wsgi python-dev` and `sudo a2enmod wsgi`
		* Changed `/etc/apache2/sites-enabled/000-default.conf`

			<VirtualHost *:80>
        		ServerName ec2-35-162-181-90.us-west-2.compute.amazonaws.com
        		ServerAdmin webmaster@localhost
        		DocumentRoot /var/www/html/catalog

		        ErrorLog ${APACHE_LOG_DIR}/error.log
        		CustomLog ${APACHE_LOG_DIR}/access.log combined

        		WSGIDaemonProcess catalog user=catalog group=catalog threads=5
        		WSGIScriptAlias / /var/www/html/catalog/catalog.wsgi
        		<Directory /var/www/html/catalog>
                	WSGIProcessGroup catalog
                	WSGIApplicationGroup %{GLOBAL}
                	Order deny,allow
                	Allow from all
        		</Directory>
			</VirtualHost>

		* `sudo apache2ctl restart`
		* `mkdir /var/www/html/catalog`
		* `sudo vim /var/www/html/catalog/catalog.wsgi`
		* Added the code to the `catalog.wsgi` file.
		```python
    	#!/usr/bin/python
		import sys
		import logging
		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0,"/var/www/html/catalog/")

		from project import app as application
		```
		as suggested from [tutorial](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps).


11. Install and configure PostgreSQL:
	* Followed [tutorial](https://help.ubuntu.com/community/PostgreSQL).
	* Installed a server locally `sudo apt-get install postgresql-client`.
	* `sudo apt-get install postgresql postgresql-contrib`
	* `sudo adduser catalog`
	* Created user `catalog` with same access rights as `grader`.
	* As user `catalog`:
    * `sudo -u postgres createuser -D -A -P catalog`
	* `sudo -u postgres createdb -O catalog catalogdb`
	* `sudo /etc/init.d/postgresql reload`
	

12. Install git, clone and setup your Catalog App project. 
	* Logged in as `catalog`
	* `sudo apt-get update`
    * `sudo apt-get install git`
    * `git init`
    * `git clone https://github.com/UnnarThorBachmann/Item-catalog.git`
    * `cp -a /home/catalog/Item-catalog/. /var/www/html/catalog`
    * Changed all referencs to sqlite database `engine = create_engine('sqlite:///catalog.db')` to `engine = create_engine('postgresql:///catalogdb')`
    * Changed the final lines of `project.py` to:
    ```python
    if __name__ == '__main__':
   		app.run()

    ``` 
    * Rand `python database_setup.py` and `python AddingToDatabase.py`
    * Made efforts to run `project.py` and installed modules when needed. I also found [this forum](https://discussions.udacity.com/t/importerror-no-module-named-psycopg2-project5/35018/5) useful
    	* `sudo apt-get install python-pip `
    	* `sudo pip install Flask `
    	* `sudo apt-get install python-psycopg2`
    	* `sudo pip install sqlalchemy`
    	* `sudo pip install Flask-SQLAlchemy`
    	* `sudo pip install --upgrade oauth2client`
    * When debugging regularly read `/var/log/apache2/error.log`
    * Added `http://ec2-35-162-181-90.us-west-2.compute.amazonaws.com/` as a valid OAuth redirect URL on facebook-developers. 

