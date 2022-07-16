# Microserver web performance evaluation using RPI64Box
This project is a collection of Ansible automated scripts to evaluate the performance of a LAMP stack using Moodle as an application testbed. 
The RPI64Box is a portable LAMP stack web server available as an ansible script via 
[https://github.com/dhuny/RPI64Box](https://github.com/dhuny/RPI64Box) or as a disk image on [https://rpi64box.com](https://rpi64box.com). The Web performance script is the client component that runs on a Ubuntu client to evaluate the performance of the RPI64Box and its hardware as a LAMP stack. The RPI64Box can operate as a microserver on a Raspberry Pi or similar hardware. This test indicates the number of users the system can support as well as the quality of the response.

## How to use it?

Clone a Ubuntu ARM-64 LTS on a storage device and boot a Raspberry Pi or similar microserver hardware to act as a  client in a client-server architecture for performance evaluation. The last tested working version was Ubuntu-20.04.4. 

Login with username `ubuntu` password `ubuntu`. Set new password to `Rpi64box4$`.

The next step is to connect the client to the server. Both wired and wireless connections are possible. For wireless connections, the RPI64Box Server is already a Wi-Fi hotspot with SSID `RPI64Box` and the password `rpi64box`. 

To connect via wired Local Area Network (LAN), connect the client and server through a router and configure their IP addresses. 

1. The steps below perform the download of a template configuration for a static IP address into /etc/netplan. The sample configuration file is available on [https://rpi64box.com/01-netcfg.yaml](https://rpi64box.com/01-netcfg.yaml)

		cd /etc/netplan && sudo wget https://rpi64box.com/01-netcfg.yaml

2. Edit the 01-netcfg.yaml and set a static IP using the following command.

		sudo nano /etc/netplan/01-netcfg.yaml
		
3. Apply the netplan with the commands below.

		sudo netplan apply
		
4. Type the commands below to update and upgrade the Linux.

		sudo apt update && sudo apt upgrade -y
		
5. Ubuntu will take a few minutes to upgrade as it may be running some updates already.
Create an `ssh.txt` file on the `boot` partition, e.g. using

		sudo touch /boot/ssh.txt

6. Install Ansible on the computer, followed by `sshpass` to enable passing SSH password to the Raspberry Pi. The following commands will install both: 

		sudo apt install sshpass ansible -y
		
7. Install an additional package that python3 may request for networking.

		sudo apt install --no-install-recommends python3-netaddr
8. Clone the web-performance repository to the user home directory. A quick way is:

		cd ~ && git clone https://github.com/dhuny/web-performance.git
		
9. Create a `keys` directory in the repository folder.

		mkdir -p ~/web-performance/keys/
10. Copy the public key to the folder web-performance under the name `id_rsa.pub`.

11. Create a public key by hitting return for blank values after launching `ssh-keygen`

		ssh-keygen -t rsa

12. Copy the public key to the folder web-performance under the name `id_rsa.pub`.

		cp ~/.ssh/id_rsa.pub ~/web-performance/keys/

13. Get the IP address of the client computer using `ifconfig` or `ip a` and update it in the `web-performance/hosts.yml` file with 

		nano ~/web-performance/hosts.yml 
		
14.	If you are connecting via a LAN, perform a manual Domain mapping entry in `/etc/hosts`.

		sudo nano /etc/hosts
		
15. For instance, if the server rpi64box.home has an ethernet ip of 192.168.0.50, add the following line.

		192.168.0.50 rpi64box.home
		
16. Edit the default.config.yml and set the necessary values for the variables.

		nano ~/web-performance/default.config.yml
		
17.	Before running the script, you may optionally load tmux with

		tmux new -s ansible

18. It is also important to check that the Moodle Server has its cron scripts working as expected. 
Login https://rpi64box.home ,  visit Site Administration and Notifications. Refresh after a few minutes to check if cron is working. 
A failing cron job will not clear the /var/www/moodledata/sessions automatically. Tests from M8 upwards may fail for this reason. 		

18. To launch the tests from xx - mm, run the ansible script with

		cd ~/web-performance  && clear && date && ansible-playbook moodletest.yml && date

The completion time of the script will vary according to the CPU speed and disk speed. 
It may take several hours. If a test hangs, it will end within a timeout set. For the XX test, the timeout is approximately 10 minutes, and for M3 – M9, the timeout is approximately 3 hours for each. 

If Tmux was used, to detach the screen and let the test run in the background, use CRTL + b followed by pressing d. To attach again, use `tmux a`. 

19. Upon completion of each test, the results will be accessible via http://client_ip_address/jmetertests/.
20. Visit https://rpi64box.com to compare the results.


### Troubleshooting & Issues

The ansible script is explicit. Some Linux distributions may require additional package installations. If the script fails, the commands can be executed directly on bash. 


## Availability

The code is available at [https://github.com/dhuny/web-performance](https://github.com/dhuny/web-performance).


## License

Copyright © 2021 onwards, Dhuny Riyad riyad@dhuny.org.

All contributions to this repository are licensed under AGPLv3 or any later version.

```
@copyright Copyright © 2021 Dhuny Riyad  (riyad@dhuny.org)
```


