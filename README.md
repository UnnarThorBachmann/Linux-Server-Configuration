# Linux-Server-Configuration



## Software installed


## Configurations made

2. Used  `ssh vagrant` to enter server as root.

3. Created a new user
..* `sudo adduser grader`
..* Password selected as `G8rA9L7`

This enabled user `grader` to gain access to the server with a pasword using

```
ssh grader@127.0.0.1 -p 2222
```

as the default port for `ssh` was 2222

4. Giving `grader` the permission to sudo upon entering password.
..* `cp /etc/sudoers.d/vagrant /etc/sudoers.d/grader`
..* `vim `ssh vagrant`
..* Changing the second line of the file to `grader All=(ALL) PASSWD:ALL`

5. Updating all currently installed packages
..* Logged in as grader and executed the following commands
..* `sudo apt-get update`
..*`sudo apt-get upgrade`

6. Change the SSH port from 22 to 2200
..* Update the vagrant file with the `commandsconfig.ssh.port = 2200`, `config.vm.network"forwarded_port", guest: 80, host: 8080` and
`config.vm.network "forwarded_port", guest: 2200, host: 2200`

..* Changed the file `/etc/ssh/sshd_config` as sudo. The lines changed were `Port 2200` and `Protocol 2` (which does not matter since 2 is the default protocol).
..* Restarted ssh with `sudo service ssh restart`.

7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
..* `sudo ufw default deny incoming`
..* `sudo ufw default allow outgoing`
..* `sudo ufw allow 2200`
..* `sudo ufw allow http`
..* `sudo ufw allow ntp`

## Configure the local timezone to UTC

My local time zone already on UTC (Greenwhich Mean Time), this is written in Iceland. This can however be changed excecuting `sudo dpkg-reconfigure tzdata`- selecting the option `None of the above` and then `UTC`. [See](http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt)

##
