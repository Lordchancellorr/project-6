- ## Lordchancellor's Documentation of Project 6

- #SUMMARY
The end product of this project was to prepare a storage infrastructure on two Linux servers and implement a basic web solution using WordPress. (WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS)- Read more here: [Wordpress](https://en.wikipedia.org/wiki/WordPress). I achieved thise by first configuring a storage subsystem for Web and Database servers based on Linux Operating System in other to have a practical experience of working with disks, partitions and volumes in Linux. Then i Installed WordPress and connected it to a remote MySQL database server. This part of the project sharpened my skills of deploying Web and DB tiers of Web solution. NB: The Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers. In this project, i had  3-Tier Setup which are 
- Laptop or PC to serve as a client
- An EC2 Linux Server as a web server 
- An EC2 Linux server as a database (DB) server
Instead of Ubuntu server used in preceding projects, [Redhat](https://www.redhat.com/en) was the virtual machine deployed on the terminal(preferably gitbash). Two instances were launched and named Database Server ans Web server respectively and new volumes were set for them. Check the images below to see what the volume looks like: ![volumes](./images/Volumes%20for%20the%20two%20servers.PNG)
- Implementation of Instructions in Step 1
After connecting to the Linux terminal for Database instance, i ran `lsblk` and the image below was the output. ![Database](./images/for%20database.PNG). `df -h` to see all mounts and free space on the server. i ran `gdisk` to create single partition for each of the three disks. i.e xvdf, xvdg and xvdh. I ran `lsblk` to view the newly configured partitions and the image below was the output. ![first partition](./images/first%20partition.PNG) Contrary to the `apt` command used in installing softwares on virtual servers, `yum` is the correct coomand for [Redhat](https://www.redhat.com/en). Progressively, `sudo pvcreate /dev/xvdf1` `sudo pvcreate /dev/xvdg1` and `sudo pvcreate /dev/xvdh1` were used to mark the three disks as physical volumes and the images belows were the output fro verification and creation. ![Physical volumes](./images/physical%20volume%20verification.PNG) ![physical volumes](./images/physical%20volume%20creation.PNG) 
- `sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1` command was used to create a volume group for the three disks and named webdata-vg. See image below ![webdata-vg](./images/successful%20volume%20group%20creation.PNG)
- Logical volume was also created for the disks. see the image for creation and verification below: ![Logical volume](./images/logical%20volume.PNG) ![logical volume](./images/logical%20volumes%20running.PNG) `sudo mkfs -t ext4 /dev/webdata-vg/app-lv and sudo mkfs -t ext4 /dev/webdata-vg/logs-lv` see ![logicalmvoes with ex4 files](./images/logical%20volumes%20with%20ex4%20filesystem.PNG) were ran on the terminal separately to format the logical volumes with ex4. `sudo mkdir -p /var/www/html` to create a directory in other to store the websites files and `sudo mkdir -p /home/recovery/logs` to store backup of log dat. /var/www/html/ was mounted on app-lv with `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`  and `sudo rsync -av /var/log/. /home/recovery/logs/` to back up all the files in the log directory on /home/recoverylogs/
After mounting, `sudo rsync -av /home/recovery/logs/. /var/log` was used to resore all the lost data. We needed to update the /etc/fstab, i ran `sudo blkid` using the UUID device. After a successful setup, the image below was the output ![setup](./images/verification%20of%20webser%20setup.PNG) ![set-up](./images/Verification%20of%20the%20setup.PNG)
## Implementation of step 2
- The same procedures followed for the web server were repeated for the database with little modifications
After connecting to a server and complying with aforementioned instructions, instead of creating apps-lv, i created db-lv and mounted it to /db directory instead of /var/www/html/.
- ## STEP 3-6
I isntalled wordpress and its dependencies by running the following commands on the database terminal after updating it with `sudo yum -y update` 
- `sudo systemctl enable httpd` 
- `sudo systemctl start httpd`
`sudo systemctl start httpd`- see the image below for the status: ![httpd](./images/httpd%20status.PNG)
For PHP Installation , run:
- `sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`
- `sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`
- `sudo yum module list php`
- `sudo yum module reset php`
- `sudo yum module enable php:remi-8.0` ( Please note, version 8.0 was the most recent version at the time of implementation and that was what i used not 7.4)
- `sudo yum install php php-opcache php-gd php-curl php-mysqlnd`
- `sudo systemctl start php-fpm`
- `sudo systemctl enable php-fpm` See the status with `sudo systemctl status php-fpm` check the image below: ![PhP status](./images/pho-fpm%20status.PNG)
- `setsebool -P httpd_execmem 1`
After that, the follwing commands to install, create a directory and copy contents to wordpress:
`mkdir wordpress`
  `cd   wordpress`
  `sudo wget http://wordpress.org/latest.tar.gz` (If your terminal does not support wget or it is showing command not founf, please click on this link to learn how to install wget then rerun this command) [wget installation](https://www.cyberciti.biz/faq/yum-install-wget-redhat-cetos-rhel-7/) 
  `sudo tar xzvf latest.tar.gz`
  `sudo rm -rf latest.tar.gz`
  `cp wordpress/wp-config-sample.php wordpress/wp-config.php`
  `cp -R wordpress /var/www/html/`
  I configured my SELinux Policies and installed mysql server and secured the installation then set it up. The images below were the output thereafter for both servers
  - ![mysql status](./images/mysq%20status%20on%20database.PNG) ![mysql](./images/mysql%20status.PNG) After configuring the databases to work with wordpress, the image below was the output: ![database](./images/proof%20of%20connection.PNG) accessed on the two servers.
  At first, the image below displayed after accessing the server on the web using the public IP address:
 ![Apache status](./images/apache%20status.PNG) but after a successful configuration, i was able to access the wordpess website created through the two servers and the images below are the final results.
 ![wordpress user creation](./images/wordpress%20user%20creation%20for%20databse.PNG)
 - ![WORDPRESS](./images/Wordpress.PNG)