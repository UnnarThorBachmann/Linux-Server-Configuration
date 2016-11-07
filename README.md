# Linux-Server-Configuration



## Software installed


## Configurations made

1. Launched my Virtual machine from my github shell on my local machine.

2. Used  `ssh -i .ssh/udacity_key root@35.162.181.90` to enter server as root.

3. Created a new user
..* `sudo adduser grader`
..* Password selected as `G8rA9L7`
..* sudo vim /etc/ssh/sshd_config
..* Changed the line `PasswordAuthentication no` to `PasswordAuthentication yes` in order to be able to access the server as grader temporarily.
..* Restarted ssh with `sudo service ssh restart`.

4. Giving `grader` the permission to sudo upon entering password.
..* `vim /etc/sudoers.d/grader`
..* Changing the second line of the file to `grader All=(ALL) PASSWD:ALL`

5. Updating all currently installed packages
..* `ssh -p 22 grader@35.162.181.90`
..* `sudo apt-get update`
..*`sudo apt-get upgrade`

6. Change the SSH port from 22 to 2200

..* Changed the file `/etc/ssh/sshd_config` as sudo. The lines changed were `Port 22` to `Port 2200`
..* Restarted ssh with `sudo service ssh restart`.

6b. Changed
	On a local virtual machine applied 'ssh_keygen' to generate private and public key. Selected `1R7p9` as a passprase. Next entered server as grader (`ssh -p 2200 grader@35.162.181.90`). In home directory
	..* mkdir .ssh
	..* touch .ssh/authorized_key
	..* Exited the server with `exit`
	..* Entered as root. Copied the public key to ~/.ssh/authorized_key
	..* Copied the key to the grader: `cp .ssh/authorized_key /home/grader/.ssh/authorized_key`
	..* Changed the line `PasswordAuthentication no` to `PasswordAuthentication yes` in order to be able to access the server as grader temporarily.
	..* Restarted ssh with `sudo service ssh restart`.

7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
..* `sudo ufw default deny incoming`
..* `sudo ufw default allow outgoing`
..* `sudo ufw allow 2200`
..* `sudo ufw allow www`
..* `sudo ufw allow ntp`

## Configure the local timezone to UTC

As sudo wrote `sudo dpkg-reconfigure tzdata` in the - selecting the option `None of the above` and then `UTC`. [See](http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt).



##
